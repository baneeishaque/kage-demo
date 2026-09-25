# Explainer: the risk model

A walkthrough of the scoring code in `dfirconsole/timeline.py` — four capped axes, a separate
confidence statement, and verdict wording that refuses to overclaim. Read
[`../architecture.md`](../architecture.md) first for the axis table this code implements, and
[`../trade-offs.md`](../trade-offs.md) for the limits it accepts.

Line references are against Kage 1.5.0.

## The model, stated in the source

```python
# Four independent axes, each capped, then summed. Capping every axis is
# what stops a single noisy rule firing 300 times from producing the same
# verdict as a genuine multi-stage intrusion.
#
#   Severity      0-45   volume and gravity of correlated detections
#   Kill chain    0-25   how many distinct ATT&CK tactics are represented
#   Reputation    0-20   indicators confirmed malicious by an external source
#   Corroboration 0-10   independent sources agreeing (YARA, live sockets)
#
# The score is a triage priority, not a proof. It is always published with
# its breakdown so a reader can disagree with a component rather than with
# an opaque number.
SEVERITY_CAP, CHAIN_CAP, REPUTATION_CAP, CORROBORATION_CAP = 45, 25, 20, 10   # :339
```

- **The design rationale lives next to the constants.** Anyone editing the caps reads *why*
  every axis is capped before touching a number.
- **The caps sum to 100**, so the score is a bounded 0–100 with no normalization step that
  could hide a component.
- **"Always published with its breakdown"** is a contract, not a slogan: `score_breakdown()`
  is the only producer of the score, and it returns the components alongside the total.

## Weights and decisive tactics

```python
LEVEL_WEIGHT = {"critical": 40, "high": 15, "medium": 4, "low": 1, "informational": 0}   # :18
```

- **Informational alerts weigh zero.** They appear in the statistics and the findings list —
  they simply do not move the score. Counting them would let volume drown signal.
- **The 40:15:4:1 ladder is steep on purpose.** One critical finding outweighs a crowd of low
  ones; the compression in the next section then makes the crowd cheap regardless.

```python
DECISIVE_TACTICS = {                                             # :343
    "Credential Access", "Lateral Movement", "Command and Control",
    "Exfiltration", "Impact", "Persistence", "Defense Evasion",
}
```

- **Breadth is not enough; the tactics must include consequences.** The comment above the set
  states the rule: "Discovery alone is an administrator; discovery plus credential access is
  not." Discovery and reconnaissance are excluded from the decisive set, so a noisy scanner
  cannot push the kill-chain axis to its cap.

## `score_breakdown` — the four axes

```python
def score_breakdown(self, external: dict | None = None) -> dict: # :348
    external = external or {}

    # -- severity: log-compressed weighted volume
    raw = sum(LEVEL_WEIGHT[level] * count for level, count in self.levels.items())
    severity = min(self.SEVERITY_CAP, round(6.5 * (raw ** 0.5))) if raw else 0

    # -- kill chain: breadth of the attack, with decisive tactics weighted
    tactics = set(self.tactics)
    decisive = tactics & self.DECISIVE_TACTICS
    chain = min(self.CHAIN_CAP, 3 * len(tactics) + 3 * len(decisive))

    # -- reputation: only externally confirmed indicators count
    malicious = external.get("malicious", 0)
    suspicious = external.get("suspicious", 0)
    reputation = min(self.REPUTATION_CAP, 10 * malicious + 3 * suspicious)

    # -- corroboration: agreement between independent sources
    corroboration = 0
    if external.get("yara_alerts"):
        corroboration += 6
    if external.get("live_connections"):
        corroboration += 4
    corroboration = min(self.CORROBORATION_CAP, corroboration)
```

- **Severity is `6.5 × √raw`, capped at 45.** The square root is the load-bearing decision: a
  thousand low-weight events (`raw = 1000`) score 205 before the cap — capped to 45, the same
  ceiling a genuine critical alert reaches at `raw ≈ 48`. Volume alone cannot buy a verdict.
- **`round()` before the cap** keeps the published number an integer and makes the cap the
  last word.
- **The kill chain counts tactics twice when decisive** (`3 × all + 3 × decisive`): eight
  tactics of which three are decisive scores 33 → capped at 25, while discovery-only tactics
  top out lower. The set algebra is one line, which is why it is one line.
- **Reputation ignores everything except external confirmation.** With no API keys the axis is
  a hard zero — the trade-offs document says so, and this line is the proof.
- **Corroboration is deliberately coarse**: +6 for YARA detections, +4 for active public
  connections, capped at 10. Independent agreement is worth points; it is not worth the whole
  score.

```python
    return {
        "score": int(total),
        "components": [
            {"name": "Severity", "value": int(severity), "max": self.SEVERITY_CAP,
             "detail": (f"{self.levels.get('critical', 0)} critical, "
                        f"{self.levels.get('high', 0)} high, "
                        f"{self.levels.get('medium', 0)} medium")},
            ...
        ],
        "confidence": self._confidence(external),
    }
```

- **Every axis ships with `value`, `max`, and a human `detail`.** The UI and the report render
  the same structure, so "why is severity 39?" is answered without a second computation.
- **`int()` at the boundary.** The math may produce floats; the published contract is integer
  components that sum to an integer score.
- **Confidence travels in the same payload as the score**, never as an afterthought — the next
  section is why that matters.

## `_confidence` — evidence quality, not severity

```python
def _confidence(self, external: dict) -> str:                    # :399
    sources = 1
    if external.get("malicious") or external.get("suspicious"):
        sources += 1
    if external.get("yara_alerts"):
        sources += 1
    if external.get("live_connections"):
        sources += 1

    coverage = external.get("coverage")
    if sources >= 3 and (coverage is None or coverage >= 45):
        return "high"
    if sources >= 2:
        return "medium"
    return "low"
```

- **Sources start at 1 (the Sigma timeline itself).** The count is *independent sources
  agreeing*: Sigma, reputation data, YARA, live connections.
- **`high` needs three sources *and* log coverage of at least 45%** (or unknown coverage). The
  docstring states the reason: "a hundred alerts from one source and no external confirmation
  is a loud but fragile finding." The coverage gate is the same idea applied to the host — a
  verdict is only worth what the machine agreed to record.
- **The ladder degrades to `low`, never to silence.** A single-source finding is still
  published; it is published as a lead.

## `verdict_for` — wording that refuses to overclaim

```python
@staticmethod
def verdict_for(score: int, confidence: str = "low") -> str:     # :425
    if score >= 70:
        return ("Compromise confirmed by multiple sources" if confidence == "high"
                else "Compromise highly likely — corroboration still limited")
    if score >= 45:
        return ("Likely compromise — containment recommended" if confidence != "low"
                else "Strong indications of compromise from a single source")
    if score >= 25:
        return "Suspicious activity requiring qualification"
    if score >= 10:
        return "Weak signals, no established compromise"
    return "Nothing conclusive"
```

- **Every band has a confidence-aware variant.** 80/100 at high confidence is "compromise
  confirmed by multiple sources"; 80/100 from Sigma alone is "highly likely — corroboration
  still limited". The same number, two honest sentences.
- **The words escalate with containment advice, not alarm.** "Containment recommended" appears
  only where the score and confidence both support action.
- **"Nothing conclusive" is a real output.** A triage tool that never says nothing is a tool
  nobody trusts the second time.

## Convenience properties

```python
@property
def risk_score(self) -> int:                                     # :421
    return self.score_breakdown()["score"]

@property
def verdict(self) -> str:                                        # :445
    breakdown = self.score_breakdown()
    return self.verdict_for(breakdown["score"], breakdown["confidence"])
```

- **Both properties recompute rather than cache.** The breakdown is cheap (a pass over counts
  and sets) and caching would risk a stale score after enrichment adds external data —
  correctness over micro-optimization.

## How the model composes

```text
timeline.csv ──► TimelineReport.ingest()   (levels, tactics, findings, IOCs)
                      │
                      ▼
   enrichment (VT/AbuseIPDB) ──► external dict ──► score_breakdown()
   YARA log ────────────────────► external dict ──►   ├─ severity  (≤45)
   live connections ────────────► external dict ──►   ├─ chain     (≤25)
                                                      ├─ reputation(≤20)
                                                      ├─ corroboration (≤10)
                                                      └─ confidence  (low|medium|high)
                      │
                      ▼
        verdict_for(score, confidence) ──► report.html / report.json / the UI gauge
```
