# Credentials

SSH passwords, private keys and passphrases are encrypted using the operating
system credential store — Keychain on macOS, Credential Manager on Windows — and
are never written to a plain-text file. This page covers what is stored where,
the choice between password and key authentication, and the deployment this
product is designed for.

There is no account system, no cloud backend and no telemetry. Credentials live
on the workstation that entered them, and nowhere else.

## What is stored, and how

A saved connection is split in two:

- **The secrets** — passwords, private key contents, passphrases, and the
  OpenStack password or application credential secret — are encrypted through the
  OS credential store and written to an encrypted file in the application's own
  data directory.
- **Everything else** — the name, type, host, port, username, environment, group
  and AI flags — is ordinary configuration and is stored as such. It is
  metadata, not credentials, and it is local to the workstation.

If the operating system cannot encrypt a credential, OpsPilot **refuses to store
it** rather than falling back to writing it in the clear. Saving fails, visibly,
instead of leaving a password in a plain-text file.

Editing a connection never re-displays a saved secret. A credential field left
blank in the edit dialog means "leave it as it was" — retyping is only needed
when you are actually changing it.

## Password or private key

SSH and Mosh show a **password** / **ssh key** toggle. The KVM hypervisor target
of a VNC connection shows it too, because in that mode those fields are the
hypervisor host's own SSH login.

- **Password** — a single field, encrypted as above.
- **SSH key** — a **private key file** path and a **passphrase (if any)**.
  OpsPilot reads the key file when you save the connection and keeps its contents
  encrypted alongside the passphrase, so the key does not have to be readable at
  every connect; it also records the path, and falls back to re-reading the file
  if the stored contents are not available. A passphrase-protected key works
  normally — the passphrase is stored the same way every other secret is.

Key authentication is the better choice for anything you connect to repeatedly: a
key can be revoked on the target, where a password has to be rotated everywhere
it is held.

Other types are narrower. RDP, VNC and FTP take a single password. Telnet, RSH,
Serial and Local take no credentials in the dialog at all.

!!! warning "Some protocols send credentials in clear text"
    Telnet, RSH and plain FTP are unencrypted by the design of those protocols.
    Storing their credentials encrypted on your workstation does not change what
    happens on the wire. Use them only on a trusted management network, and
    prefer SSH, SFTP or FTPS where the target supports it.

## Recommended deployment

Run OpsPilot against your targets over a VPN or a trusted management network.
That is the deployment this product is designed for, and it is what keeps the
path between your workstation and your estate under your own control.

It also matches how the rest of the product behaves. OpsPilot installs nothing on
target hosts — no agent, no daemon, no sidecar — and opens no inbound listener
for them. The only reachability it needs is the reachability you already have.

!!! note "Private or VPN-only is not air-gapped"
    With a cloud AI provider, your *workstation* still reaches the internet. That
    is private or VPN-only operation. For a genuinely air-gapped deployment the
    model has to be local as well. See
    [Running offline with Ollama](../ai/offline-ollama.md).

One platform-specific credential note: on Linux, an RDP password is handed to the
external FreeRDP client on its command line, because that is the only
non-interactive way it accepts one, so it is visible to anything on that machine
that can list your processes. Leave the password field empty and let the client
prompt if that matters in your threat model. See
[Remote desktop](remote-desktop.md).

## See also

- [Add a connection](adding-connections.md) — where credentials are entered
- [Security model](../safety/security-model.md) — the threat model these choices
  come from
- [Hardening checklist](../safety/hardening.md) — what to tighten before a
  rollout
- [Remote desktop (RDP & VNC)](remote-desktop.md) — the per-platform client
  differences
