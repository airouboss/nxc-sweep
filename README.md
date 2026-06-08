# nxc-sweep

A bash wrapper for [NetExec](https://github.com/Pennyw0rth/NetExec) that sweeps a target across all common pentest protocols in one command.

---

## Requirements

- NetExec (`nxc`) in `$PATH`
- `netcat` (`nc`)
- Bash

---

## Install

```bash
curl -sSL 'https://raw.githubusercontent.com/YOUR_USERNAME/nxc-sweep/main/nxc-sweep' -o nxc-sweep && chmod +x nxc-sweep && sudo mv nxc-sweep /usr/local/bin/
```

---

## Usage

```bash
# Single creds
nxc-sweep <IP> -u admin -p Password123

# Local auth
nxc-sweep <IP> -u admin -p Password123 --local-auth

# Password spray (pass files — spray mode auto-detected)
nxc-sweep <IP> -u users.txt -p passwords.txt

# No-auth recon (null sessions, anon binds, SMB vuln sweep)
nxc-sweep <IP> --no-auth
```

---

## Protocols

| Port | Protocol |
|------|----------|
| 445 / 139 | SMB |
| 5985 | WinRM |
| 3389 | RDP |
| 1433 | MSSQL |
| 21 | FTP |
| 22 | SSH |
| 389 | LDAP |
| 5432 | PostgreSQL |
| 3306 | MySQL |
| 623 | IPMI |
| 80 / 443 | HTTP/HTTPS |
| 135 | WMI |
| 5900 | VNC |

---

## Notes

- Ports are pre-checked with `nc` — closed ports are skipped instantly
- Spray mode auto-detects when a file is passed and adds `--no-bruteforce --continue-on-success` automatically
- RDP always runs with `--no-bruteforce` to prevent lockouts
- No-auth mode runs SMB vuln modules: Zerologon, MS17-010, SMBGhost, PrintNightmare

---

> For authorised penetration testing only.
