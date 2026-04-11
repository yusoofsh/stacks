# yusoofsh.cloud stacks

This directory contains Uncloud-friendly Compose stacks for:

- `adguard` for `DoH`, `DoT`, and `DoQ`
- `moltis` for an AI control-plane UI
- `dockhand` for Git-backed webhook auto-deploys

These files are written to work well with:

- `uc deploy -f compose.yaml`
- Dockhand stack import from the filesystem

## Layout

- `adguard/compose.yaml`
- `adguard/conf/AdGuardHome.yaml`
- `adguard/tls/`
- `adguard/issue-wildcard-cert.sh`
- `moltis/compose.yaml`
- `moltis/.env.example`
- `dockhand/compose.yaml`

## Cloudflare records

Create these records in Cloudflare:

- `A doh.yusoofsh.cloud -> <your-vps-ip>` as `DNS only`
- `A dot.yusoofsh.cloud -> <your-vps-ip>` as `DNS only`
- `A doq.yusoofsh.cloud -> <your-vps-ip>` as `DNS only`
- `A adguard.yusoofsh.cloud -> <your-vps-ip>` as `DNS only` or no public record if you only want private admin access
- `A moltis.yusoofsh.cloud -> <your-vps-ip>` as `Proxied`
- `A dockhand.yusoofsh.cloud -> <your-vps-ip>` as `Proxied` if you want a public UI, otherwise keep it private
- `A *.yusoofsh.cloud -> <your-vps-ip>` as `Proxied`

Explicit host records override the wildcard, so `doh`, `dot`, and `doq` stay direct while normal apps use the wildcard.

## AdGuard TLS

`DoT` and `DoQ` need a real public certificate that AdGuard presents directly.

The helper script `adguard/issue-wildcard-cert.sh` issues a wildcard certificate with a Cloudflare DNS challenge and writes
the files to the expected location.

## Suggested rollout

1. Copy `moltis/.env.example` to `moltis/.env` and set the real values.
2. Create `adguard/cloudflare.ini` from the example and add a Cloudflare API token.
3. Run `adguard/issue-wildcard-cert.sh` to populate `adguard/tls/`.
4. Deploy `adguard/compose.yaml`.
5. Deploy `moltis/compose.yaml`.
6. Deploy `dockhand/compose.yaml`.

## Notes

- `moltis` runs behind Uncloud/Caddy with `--no-tls`, matching Moltis' official reverse-proxy guidance.
- `dockhand` is mounted against the same host paths it needs to manage, which matches Dockhand's documented path
  requirements for reliable relative-volume handling.
- If you later want browser-based OAuth flows in Moltis from a remote device, you may also need to publish port `1455`
  directly and use a DNS-only record for that callback path or rely on CLI auth instead.
