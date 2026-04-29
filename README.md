# bail-themes

OTA theme bundles for [Bail](https://github.com/stinsonrecon/MakeMeBusy) call screen themes.

This is a **public** companion repo for the (private) Bail app. Theme files here are fetched by the app at runtime via WorkManager, verified with Ed25519 signatures, and applied without app updates.

## Structure

```
themes/                          ← this repo's root
├── manifest.json                ← list of all themes + hashes/versions
├── manifest.json.sig            ← Ed25519 signature of manifest.json
├── whatsapp.json                ← Red Team theme spec (or whatsapp.zip for Blue Team)
├── whatsapp.json.sig            ← signature
├── messenger.json
├── messenger.json.sig
└── ... (one pair per theme)
```

## OTA URL pattern

Bail app fetches:

```
https://raw.githubusercontent.com/stinsonrecon/bail-themes/main/manifest.json
https://raw.githubusercontent.com/stinsonrecon/bail-themes/main/<theme>.json
https://raw.githubusercontent.com/stinsonrecon/bail-themes/main/<theme>.json.sig
```

## Workflow (developer)

1. Edit a theme spec (e.g., `whatsapp.json`).
2. Run `./tools/sign-theme.sh whatsapp.json` (script lives in main app repo) to regenerate `whatsapp.json.sig`.
3. Update `manifest.json` if adding/removing themes (hash auto-updates per file).
4. Commit + push:
   ```
   git add whatsapp.json whatsapp.json.sig manifest.json
   git commit -m "feat(whatsapp): redesign v3 — match WhatsApp 2.25.x"
   git push
   ```
5. App users sync within 24h (or via manual "Check for updates" button).

## Signing

Ed25519 keypair:
- **Private key**: kept offline at `~/.bail-theme-signing/private.key` on developer's machine. NEVER committed.
- **Public key**: embedded in Bail app source (`secrets.xml`) at build time.

If keys are ever compromised: rotate by generating new pair, updating app's embedded public key, releasing new app version, and re-signing all theme files with new private key.

## Schema versioning

Theme JSON files include `schemaVersion` field. App ignores theme files with unsupported schema version (logs warning). Bump schema version only when introducing breaking changes.

## License

MIT — see main app repo for full license.
