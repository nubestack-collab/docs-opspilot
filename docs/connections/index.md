# Connections

A connection is a saved definition of something you want to reach — a server, a
switch, a Windows desktop, an FTP server, a bucket, a serial console or your own
shell. Ten connection types ship. Opening one starts a session, and a session is
what the AI panel and the safety policy apply to.

Two fields on every connection carry more than their size in the dialog suggests:
the **environment** tag, which colour-codes the connection and is how safety
policy is scoped in practice, and the AI toggle, which decides whether the
assistant may see the session at all.

## Pages in this section

- [Add a connection](adding-connections.md) — the end-to-end task: **+ new
  connection**, picking a type, the host, port, username and authentication
  fields, then environment, AI access and saving.
- [Connection types](connection-types.md) — all ten types, what each one opens
  (in-app terminal, embedded desktop, file browser or an external application),
  the default ports, and which of them are unencrypted.
- [Hypervisor consoles](hypervisor-consoles.md) — attaching to a virtual
  machine's console through KVM/libvirt or OpenStack Nova, for the machine that
  will not boot or has no SSH at all.
- [Remote desktop (RDP & VNC)](remote-desktop.md) — graphical sessions, the
  Windows domain and fresh-session options, and which RDP client each platform
  uses.
- [Groups & environments](organising.md) — how groups carry Command Safety and
  Data Handling profiles, what environment colours are for, and the
  per-connection AI badge.
- [Credentials](credentials.md) — where passwords, private keys and passphrases
  are stored, password versus key authentication, and the recommended
  deployment.

## See also

- [Quickstart](../getting-started/quickstart.md) — first connection to first AI
  answer
- [Connection fields reference](../reference/connection-fields.md) — every field,
  per type
- [Sessions & tabs](../workspace/sessions.md) — what happens once a connection is
  open
