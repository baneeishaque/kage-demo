# Kage field kit

An independent field guide and publishing kit for **Kage** — the open-source Windows DFIR
triage console — covering two operator workflows end to end:

- **Demo mode** — rehearse the entire eleven-step triage chain against a synthetic intrusion
  without touching the host.
- **Headless over SSH** — run the console elevated on a suspect Windows machine while you work
  from your own, with the evidence never leaving the suspect's disk.

This repository does not contain the tool. It is documentation produced from a real demo run,
plus the content kit that publishes it: one canonical article, platform-specific adaptations,
a Medium funnel, four training programs, and a reproducible 37-locale video pipeline.

**The tool: [github.com/karim852/Kage-DFIR-toolkit](https://github.com/karim852/Kage-DFIR-toolkit)**
**Live demo: [demo-kumo-kage.vercel.app](https://demo-kumo-kage.vercel.app/)**

## Contents

| Path                                                             | What it is                                                        |
| ---------------------------------------------------------------- | ----------------------------------------------------------------- |
| [`docs/architecture.md`](docs/architecture.md)                   | How the eleven-step chain, scoring, and seals fit together        |
| [`docs/trade-offs.md`](docs/trade-offs.md)                       | The limits worth knowing before pointing it at a host             |
| [`docs/demo-mode.md`](docs/demo-mode.md)                         | Running and reading the synthetic intrusion                       |
| [`docs/headless-ssh.md`](docs/headless-ssh.md)                   | Elevated, headless operation over SSH — full command reference    |
| [`docs/explainers/`](docs/explainers/)                           | Line-by-line code walkthroughs: chain and seals, scoring, server  |
| [`content/`](content/README.md)                                  | Publishing kit: canonical article + platform adaptations          |
| [`content/youtube/`](content/youtube/README.md)                  | Video pipeline: 37 locales, 20 narrations, one master MKV         |
| [`content/funnel-medium/`](content/funnel-medium/README.md)      | Medium funnel: deep dive + nine platform adaptations              |
| [`content/training/`](content/training/README.md)                | Training programs: webinar, free course, workshop, pro course     |

## Quick start — demo mode

On any machine (macOS, Linux, or Windows), with Python 3.10+:

```bash
git clone https://github.com/karim852/Kage-DFIR-toolkit.git
cd Kage-DFIR-toolkit
python -m dfirconsole --demo
```

Open <http://127.0.0.1:8787>, sign in as `admin`, and run the chain. Demo mode builds a
synthetic intrusion — encoded PowerShell, credential theft, persistence, command-and-control,
shadow copies deleted — and runs every step against it. Nothing on the machine is read,
scanned, or changed. On macOS and Linux the demo is the default mode even without the flag.

Details, expected output, and what each step demonstrates: [`docs/demo-mode.md`](docs/demo-mode.md).

## Quick start — headless over SSH

On a Windows host you own or have **written permission** to examine, with OpenSSH Server
installed and the console repo on disk:

```powershell
schtasks /Create /TN KageHeadless /TR "cmd /c cd /d C:\Kage && python -m dfirconsole" ^
  /SC ONCE /ST 23:59 /RL HIGHEST /F
schtasks /Run /TN KageHeadless
```

From your own machine:

```bash
ssh -N -o ServerAliveInterval=30 -o ExitOnForwardFailure=yes -L 8787:127.0.0.1:8787 user@host
```

Then open <http://127.0.0.1:8787>. The console binds to loopback only — it has no
authentication by design, so it is reached through the tunnel and never exposed. The full
reference, including elevation, firewall, and evidence-handling notes:
[`docs/headless-ssh.md`](docs/headless-ssh.md).

## Rebuild the video

The pipeline needs macOS `say` (the narration stage), `ffmpeg`/`ffprobe`, and Node.js with the
repository's dev dependencies installed:

```bash
npm install
python3 content/youtube/build-scenes.py --check   # validate the 37 translations
python3 content/youtube/build-scenes.py           # write scenes.json
python3 content/youtube/build-video.py --all      # every stage, every locale (hours)
```

See [`content/youtube/README.md`](content/youtube/README.md) for the locale roster, voices,
stage-by-stage commands, and the master MKV layout.

## Credit and independence

Kage is created and maintained by [k0r1m](https://github.com/karim852) and released under the
MIT license. This repository is an independent guide and kit — not affiliated with or endorsed
by the Kage author. Use it only on hosts you own or are explicitly authorized to examine.

## License

[MIT](LICENSE) — for this guide and kit. Kage itself is separately MIT-licensed by its author.
