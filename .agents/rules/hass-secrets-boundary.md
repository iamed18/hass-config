# Home Assistant Secrets & Credential Boundary

Never read, `cat`, `grep`, or print raw contents of secret-bearing files—any tool output enters model context and is transmitted to the LLM provider.

## Restricted Files (Never Print Raw Contents)
- `secrets.yaml`, `.env`, `.env.*`
- `*.key`, `*.key.*`, `*.pem` (except public certs when inspecting expiry/subject only), `*.crt`, `*.p12`, `*token*`, `access_token.txt`
- `/config/.storage/*` (especially `core.config_entries`, `auth*`, `http.auth`, `.cloud/*`)
- `~/.ssh/id_*` (private keys; only `*.pub` may be read if needed)

## Safe Inspection Patterns (Names/Metadata Only)
- **`secrets.yaml`:** Only list key names:
  `awk -F: '/^[A-Za-z0-9_]+:/{print $1}' /config/secrets.yaml`
- **`.env`:** Only list variable names (`cut -d= -f1 .env`), or load into a shell/env variable inline without echoing.
- **`.storage/core.config_entries`:** Never `grep` raw JSON lines (they contain OAuth tokens and app passwords). Parse with Python and print only non-secret metadata (`domain`, `title`, `entry_id`, `disabled_by`, `source`, `pref_disable_polling`)—never `data` or `options`.
- **Add-on configs (`ha apps info <slug>`):** Redact or omit `password`, `token`, `secret`, and `api_key` fields.
- **Git / GitHub (`iamed18/hass-config`):** Keep all restricted files in `.gitignore` and use `!secret <KEY>` for any PII/credentials in tracked YAML files.
