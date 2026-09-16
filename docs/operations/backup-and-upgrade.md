# Backup & upgrade

Everything OpsPilot remembers is a file on your own workstation. There is no cloud backend
and no cloud backup to fall back on, so the copy you take before an upgrade is the only
copy there is.

## What to back up

It all lives in one directory: the application's per-user data directory. Copy the whole
directory rather than individual files; the stores reference each other by id, and a
partial restore leaves connections pointing at groups and profiles that are no longer
there.

| Data | File |
|---|---|
| Connections | `connections.json` |
| Groups | `groups.json` |
| Environments | `environments.json` |
| Command Safety profiles | `command-safety-profiles.json` |
| Data Handling profiles | `redaction-profiles.json` |
| Application settings | `settings.json` |
| File-transfer history | `transfer-history.json` |
| Encrypted credentials | `credentials.enc.json` |
| ChatGPT tunnel runtime key | `openai-tunnel/runtime-key.enc` |

`settings.json` holds the non-secret settings — the AI provider configuration, the
auto-run and confirmation toggles, the MCP connector state and the tunnel metadata. It does
not hold connection secrets; those are in the two encrypted files.

### Finding the directory

Identify the directory by its contents. Look under your user profile's application-data
location — the standard per-user location for your platform — for a folder holding
`connections.json` and `settings.json` side by side. That folder is the one to copy. If you
need the exact location confirmed for a build you are packaging or auditing, ask support:
a wrong directory produces a backup that silently restores nothing.

## What a backup does not contain

- **The keys that decrypt your credentials.** SSH passwords, private keys, key passphrases
  and the tunnel runtime key are encrypted through the operating system's own credential
  store before they are written. `credentials.enc.json` and `runtime-key.enc` are
  ciphertext, and the material that decrypts them belongs to the OS user account on that
  machine. Restoring those files onto a different workstation, or a rebuilt one, will not
  give you working credentials — you re-enter them.
- **Terminal scrollback.** Session output lives in memory only and is never written to disk
  by OpsPilot. There is nothing to back up and nothing to purge.

!!! warning "Test the restore, not just the backup"
    A copy you have never restored is an assumption. Restore it onto the same machine into
    a spare directory at least once, and confirm the connections, groups and both profile
    sets come back intact.

## Before an upgrade

1. Close OpsPilot. Copying the directory while the application is writing to it can
   capture a half-written store.
2. Copy the user-data directory somewhere outside it.
3. On Windows, also quit any assistant that OpsPilot is connected to over stdio — Claude
   Desktop and VS Code both launch OpsPilot's MCP bridge using the installed executable, and
   a running bridge process can prevent the installer from replacing that executable.
4. Note the build you are on, so you know what you upgraded from. Take it from the
   installer or download you ran.

## Upgrading

Install the new build over the old one. There is no in-app updater, no licence server check
and no migration step to run.

**Verify the published SHA-256 hash of the download before you run it.**

=== "Windows"

    The installer is an NSIS package that installs **per-machine**. Expect a UAC prompt,
    and note that you can choose the installation directory. Run it over the existing
    installation; your user-data directory is untouched by the installer.

    The Windows installer is also the only one that carries the bundled FreeRDP client, so
    an upgrade here replaces the RDP engine as well as the application.

=== "macOS"

    Builds ship as a DMG and a ZIP, for both Intel and Apple Silicon. Quit OpsPilot,
    replace the application with the new one, then launch it again.

=== "Linux"

    The build is an AppImage. Replace the old AppImage file with the new one and make it
    executable. Nothing else on the system is modified.

## After an upgrade

- Confirm the application launches and your connections, groups and profiles are all
  present.
- Open **Settings → AI Providers** and use **Test Connection** on your active provider.
- Check that both profile sets are present and still assigned to the groups you expect.
  Profiles are replacements rather than additions, so a group that has lost its assignment
  falls back to `Default` — which may be stricter or looser than what you intended.
- If you use an assistant, reconnect it and confirm it can see OpsPilot again. Claude
  Desktop and VS Code read MCP configuration at startup.

## See also

- [Install](../getting-started/install.md) — first-time installation and requirements
- [Administration & rollout](administration.md) — phasing a rollout, and where profile
  configuration is held
- [Hardening checklist](../safety/hardening.md) — including installer hash verification
- [Troubleshooting](troubleshooting.md) — if something does not come back after an upgrade
