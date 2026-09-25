# Headless over SSH

Run the console **elevated and headless on a remote Windows host** while you work from your
own machine. The evidence stays on the host's disk; only the UI travels — over an SSH tunnel.
This is the workflow for triaging a machine you can reach but should not sit in front of, and
for keeping a colleague's workstation usable while it is examined.

> **Authorization first.** Collecting evidence and running scanners on a host requires the
> owner's **written** permission and an agreed scope. Get that before anything below.

## Why this shape

- The console is a local web app (`127.0.0.1:8787`), so it needs **no desktop session** — a
  scheduled task can start it hidden, elevated, and it survives your disconnect.
- An SSH session on Windows is **non-interactive**: UAC cannot prompt, so `runas`-style
  elevation is impossible. A Task Scheduler task with `/RL HIGHEST` is the clean way to get
  an elevated process without a prompt.
- The console has **no authentication** (see `trade-offs.md`): the SSH tunnel is the security
  boundary. Never bind it to `0.0.0.0`.

## Step 1 — Install OpenSSH Server on the host

In an elevated PowerShell on the host (or via any existing remote-management channel):

```powershell
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
Set-Service -Name sshd -StartupType Automatic
Start-Service sshd
Get-Service sshd
```

The capability creates the firewall rule (`OpenSSH-Server-In-TCP`) itself. If your
environment manages the firewall centrally, allow inbound TCP 22 from your machine only.

## Step 2 — Key-based authentication

Generate a key pair on **your** machine if you do not have one (`ssh-keygen -t ed25519`), then
place the **public** key on the host. Windows OpenSSH reads two different files:

| Account type                | Authorized keys file                                        |
| --------------------------- | ----------------------------------------------------------- |
| Standard user               | `C:\Users\<user>\.ssh\authorized_keys`                      |
| Administrator               | `C:\ProgramData\ssh\administrators_authorized_keys`         |

The default `sshd_config` has a `Match Group administrators` block that points at the
`ProgramData` file — a key appended to the wrong file is silently ignored. The
`administrators_authorized_keys` file must be readable only by Administrators and SYSTEM:

```powershell
icacls.exe "C:\ProgramData\ssh\administrators_authorized_keys" /inheritance:r `
  /grant "Administrators:F" /grant "SYSTEM:F"
```

Optional — make PowerShell the default remote shell (nicer than `cmd`):

```powershell
New-ItemProperty -Path "HKLM:\SOFTWARE\OpenSSH" -Name DefaultShell `
  -Value "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" `
  -PropertyType String -Force
```

Verify from your machine: `ssh <user>@<host> whoami` — it must print the account name without
a password prompt.

## Step 3 — Put the console on the host

```powershell
git clone https://github.com/karim852/Kage-DFIR-toolkit.git C:\Kage
python -m pip install -r C:\Kage\requirements.txt
python C:\Kage\preflight.py
```

Python 3.10+ must be installed system-wide (not per-user). `preflight.py` is the tool's own
readiness check: it verifies the Python version, administrator rights, free space ("a
collection easily reaches several GB"), and which tools are present.

## Step 4 — Start it elevated and headless (the scheduled-task pattern)

```cmd
schtasks /Create /TN KageHeadless /TR "cmd /c cd /d C:\Kage && python -m dfirconsole" /SC ONCE /ST 23:59 /RL HIGHEST /F
schtasks /Run /TN KageHeadless
```

What each piece does:

- **`/RL HIGHEST`** — runs with the account's highest privileges. For an administrator
  account that is an **elevated process with no UAC prompt**, even though the SSH session
  that created the task was not elevated. (For a standard user it is a no-op — elevation
  requires an administrator account.)
- **`/SC ONCE /ST 23:59`** — the task is only a container for on-demand runs; the schedule is
  irrelevant because you always trigger it manually with `schtasks /Run`. The fixed time just
  satisfies the scheduler.
- **`cmd /c cd /d C:\Kage && python -m dfirconsole`** — sets the working directory (the
  workspace defaults to it) and starts the console. Task Scheduler runs it **hidden**: no
  window appears on anyone's desktop.
- If the task cannot find Python, use its full path:
  `/TR "cmd /c cd /d C:\Kage && C:\Python312\python.exe -m dfirconsole"`.

Variants:

```cmd
:: rehearse first — synthetic evidence, nothing touched
schtasks /Create /TN KageDemo /TR "cmd /c cd /d C:\Kage && python -m dfirconsole --demo" /SC ONCE /ST 23:59 /RL HIGHEST /F

:: non-default port (the server defaults to 127.0.0.1:8787)
schtasks /Create /TN KageHeadless /TR "cmd /c cd /d C:\Kage && python -m dfirconsole --port 8788" /SC ONCE /ST 23:59 /RL HIGHEST /F
```

Verify on the host (over SSH):

```powershell
curl.exe -s -o NUL -w "%{http_code}`n" http://127.0.0.1:8787   # expect 200
netstat -ano | findstr :8787
```

The console prints its banner and the line `Uvicorn running on http://127.0.0.1:8787` into
the session log — that line is the definitive "it is up".

## Step 5 — Tunnel from your machine

```bash
ssh -N -o ServerAliveInterval=30 -o ExitOnForwardFailure=yes -L 8787:127.0.0.1:8787 <user>@<host>
```

- `-N` — no remote shell; just the forward.
- `ServerAliveInterval=30` — keeps the tunnel alive across idle periods and NAT timeouts.
- `ExitOnForwardFailure=yes` — fail fast if local port 8787 is already taken.
- `-L 8787:127.0.0.1:8787` — your local 8787 → the host's loopback 8787.

Open <http://127.0.0.1:8787> in your own browser. **No authentication is asked for and none
exists** — the tunnel is the boundary. Keep it open only while you work.

## Step 6 — Warm-up checklist

1. In Settings, set the **case reference** and **analyst** name; leave the workspace default
   (the launch folder) unless you have a reason.
2. Do one **demo run** (tick Demonstration mode) to confirm the whole loop: chain → verdict →
   report. It takes seconds.
3. Untick demo, tick the steps you want, and **Run the chain**. Expect step 04 (collection) to
   dominate the wall-clock time.
4. You can close the browser tab — the server keeps running. Reconnect the tunnel and refresh
   to pick the stream back up.

## During and after the run

- Everything lands on the **host's** disk: `evidence/`, `output/`, and the session log. The
  browser only ever received the live stream.
- The report carries the **seals** — each step's SHA-256 and what it covers. Verify the
  hashes before and after any transfer.
- To take the results with you, copy them over the same SSH channel
  (`scp -r <user>@<host>:C:/Kage/output ./kage-output`) and record who copied what and when.
  Keep the host's originals untouched.

## Teardown

```cmd
:: stop the running instance and remove the task
schtasks /End /TN KageHeadless
schtasks /Delete /TN KageHeadless /F
```

If you cannot find the task instance, the process is the one listening on 8787 —
`netstat -ano | findstr :8787` gives the PID for a targeted `taskkill /PID <pid>`.

Two cleanups people forget:

- **Step 02 added a Defender exclusion** for the workspace. It persists after the run. Remove
  it when policy requires: `Remove-MpPreference -ExclusionPath "C:\Kage"` (elevated).
- **The SSH key you installed** was for this investigation. Remove the line from the
  authorized-keys file if the access was one-time.

## Troubleshooting

| Symptom                                   | Likely cause and fix                                                          |
| ----------------------------------------- | ----------------------------------------------------------------------------- |
| `curl` on the host: connection refused    | The task is not running, or bound to a different port — check `netstat`; rerun `schtasks /Run` |
| SSH connects but the browser shows nothing | Tunnel is fine but the server is down, or you forwarded the wrong port         |
| `ssh` asks for a password                 | Key in the wrong file (standard vs `administrators_authorized_keys`) or wrong ACLs |
| Task runs, then dies instantly            | Python not on the task's PATH — use the full `python.exe` path in `/TR`        |
| Defender quarantines a tool mid-run       | Step 02 (exclusion) was unticked or the run was not elevated                   |
| Tunnel drops during the long collection   | Reconnect with `ServerAliveInterval=30`; the server kept running — refresh     |

## Safety rules (non-negotiable)

1. **Never** launch with `--host 0.0.0.0`. There is no authentication; a network-reachable
   console is a remote-control DFIR tool for anyone who finds it.
2. Use the tunnel for remote access; close it when you are done.
3. Written authorization, an agreed scope, and a documented evidence hand-off.
4. Leave the host's originals in place; copy, do not move.
