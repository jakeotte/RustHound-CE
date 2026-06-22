# RustHound-CE (obfuscation fork)

Fork of [g0h4n/RustHound-CE](https://github.com/g0h4n/RustHound-CE) focused on OPSEC-safe BloodHound collection.

## Changes

`--obfuscated` replaces the broad `(objectClass=*)` query with targeted per-type queries:

- Exact attribute lists per object type — no `*`
- Non-overlapping filters, no object returned twice
- SD control scoped only to types that parse `nTSecurityDescriptor`
- Query order randomized each run
- Page size randomized (100–300) per query
- Random jitter between queries (`--jitter-min` / `--jitter-max`, default 500–3000ms)

## Usage

```bash
rusthound-ce -d DOMAIN.LOCAL -u user@domain.local -p 'pass' -z --obfuscated
rusthound-ce -d DOMAIN.LOCAL -u user@domain.local -p 'pass' -z --obfuscated --jitter-min 1000 --jitter-max 5000
```

## Source IP / DNS alerts

DCs log the source IP of every LDAP bind. Queries from an IP not registered in DNS or
AD computer objects will alert. Two options without creating new records:

**1. Run the binary directly on a domain-joined host**
Upload the Windows build and execute it there — source IP is the machine itself.

**2. Proxy through a domain-joined host via SSH**
`ssh.exe` ships with Windows 10+ and supports SOCKS5 proxying:

```bash
# on the Windows host (or via your C2)
ssh.exe -N -D 1080 user@jumphost  # or reverse: attacker SSHes in and runs -D locally

# on attack machine
proxychains rusthound-ce -d DOMAIN.LOCAL -u user@domain.local -p 'pass' -z --obfuscated -i DC_IP
```

DC sees LDAP traffic sourced from the Windows machine. proxychains intercepts at the socket layer and works with Rust binaries.

## Build

```bash
make release
cargo build --release --no-default-features --features nogssapi  # macOS
```
