# RustHound-CE (obfuscation fork)

Fork of [g0h4n/RustHound-CE](https://github.com/g0h4n/RustHound-CE) focused on OPSEC-safe BloodHound collection.

## What's different

`--obfuscated` flag replaces the standard broad LDAP query with targeted per-type queries designed to avoid common detection signatures:

- Specific attribute lists per object type instead of `*`
- Non-overlapping filters — no object appears in two result sets
- SD control scoped only to types that parse `nTSecurityDescriptor`
- Randomized query order per run
- Randomized page size (100–300) per query
- Random jitter between queries (`--jitter-min` / `--jitter-max`, default 500–3000ms)

## Usage

```bash
# Standard collection
rusthound-ce -d DOMAIN.LOCAL -u user@domain.local -p 'pass' -z

# OPSEC-safe collection
rusthound-ce -d DOMAIN.LOCAL -u user@domain.local -p 'pass' -z --obfuscated

# Tune jitter (milliseconds)
rusthound-ce -d DOMAIN.LOCAL -u user@domain.local -p 'pass' -z --obfuscated --jitter-min 1000 --jitter-max 5000
```

## Build

```bash
make release
# or
cargo build --release --no-default-features --features nogssapi  # macOS
```
