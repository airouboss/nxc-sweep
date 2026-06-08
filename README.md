# nxc-sweep

A bash wrapper for [NetExec](https://github.com/Pennyw0rth/NetExec) that sweeps a target across all common pentest protocols in one shot. Built for HTB, OSCP prep, and internal AD engagements.

Instead of running `nxc smb`, `nxc winrm`, `nxc rdp` etc. one by one, nxc-sweep pre-validates each port with netcat then fires the relevant nxc command automatically — preserving native NetExec colours and output throughout.

---

## Features

- Port pre-validation via `nc` — skips closed ports instantly, no wasted time
- Two modes: authenticated sweep and no-auth recon pass
- SMB vuln sweep in no-auth mode (Zerologon, MS17-010, SMBGhost, PrintNightmare)
- Smart flag filtering — strips incompatible flags like `--local-auth` per protocol automatically
- RDP lockout protection via `--no-bruteforce` baked in by default
- Native NetExec colours preserved throughout

---

## Requirements

- [NetExec](https://github.com/Pennyw0rth/NetExec) installed and in `$PATH`
- `netcat` (`nc`) available
- Bash

---

## Installation

```bash
curl -sSL 'https://raw.githubusercontent.com/YOUR_USERNAME/nxc-sweep/main/nxc-sweep' -o nxc-sweep && chmod +x nxc-sweep && sudo mv nxc-sweep /usr/local/bin/
```

> Replace `YOUR_USERNAME` with your GitHub username.

---

## Usage

**Authenticated sweep:**
```bash
nxc-sweep <IP> -u <username> -p <password>
```

**With local auth:**
```bash
nxc-sweep <IP> -u <username> -p <password> --local-auth
```

**No-auth recon pass:**
```bash
nxc-sweep <IP> --no-auth
```

---

## Examples

```bash
# Credentialed sweep against a domain target
nxc-sweep 10.10.10.100 -u john -p Password123

# Local auth sweep (non-domain machine)
nxc-sweep 10.10.10.100 -u administrator -p Password123 --local-auth

# No-auth pass — null sessions, anon binds, IPMI, SMB vuln modules
nxc-sweep 10.10.10.100 --no-auth
```

---

## Protocols Covered

| Port | Protocol | Notes |
|------|----------|-------|
| 445 | SMB | Share enumeration |
| 139 | SMB (NetBIOS) | Legacy SMB fallback |
| 5985 | WinRM | Remote management |
| 3389 | RDP | No-bruteforce by default |
| 1433 | MSSQL | Lists databases |
| 21 | FTP | Directory listing, anon login |
| 22 | SSH | OpenSSH on Windows/Linux |
| 389 | LDAP | User enum, password-not-required |
| 5432 | PostgreSQL | Auth check |
| 3306 | MySQL | Auth check |
| 623 | IPMI | Cipher 0 / hash grab |
| 80 | HTTP | Basic auth panel check |
| 443 | HTTPS | Basic auth panel check |
| 135 | WMI | RPC lateral movement path |
| 5900 | VNC | Auth check, no-auth detection |

---

## No-Auth Mode

Running `--no-auth` performs a recon-only pass using empty or anonymous credentials. On top of the standard protocol checks it also runs the following SMB vulnerability modules if port 445 is open:

- `zerologon`
- `ms17-010`
- `smbghost`
- `printnightmare`

These require no credentials and are among the first checks on any internal engagement.

---

## Adding More Protocols

The script is easily extensible. To add a new protocol just follow this pattern at the bottom of the protocols section:

```bash
check_and_run <PORT> "<protocol>" [extra nxc flags]
```

For example:
```bash
check_and_run 5986 "winrm"   # WinRM over HTTPS
```

---

## Disclaimer

This tool is intended for authorised penetration testing and security research only. Always ensure you have explicit written permission before running against any target.
