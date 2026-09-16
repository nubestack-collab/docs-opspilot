# Built-in tools

Seven tools ship inside the workbench, so the routine work that usually sends you to
another application happens in the session you are already in. None of them installs
anything on the target host.

| Tool | What it does |
|---|---|
| **Port Forward** | Local and remote SSH tunnels, managed from the UI |
| **File Manager** | Browse, upload and download over SFTP/FTP without leaving the session |
| **Snippets** | Save and re-run command templates |
| **System Monitor** | Live resource figures for the connected host |
| **Insights** | A summary of the current run — open sessions, counters, latest monitor sample |
| **Themes** | Terminal and UI theming, including the OpsPilot palette |
| **Search** | Search within terminal scrollback |

## Port Forward

The Port Forwarding panel creates and manages SSH tunnels on the active session, with no
`ssh -L` invocation to remember. Pick a direction, fill in the ports, and click **Add
Tunnel**:

- A **local forward** listens on `127.0.0.1:<local port>` on your workstation and
  tunnels connections to `<remote host>:<remote port>` as resolved by the SSH server.
  The remote host is a real hostname or address from the server's point of view — not
  `localhost`, which would mean the server connecting to itself.
- A **remote forward** asks the SSH server to listen on `0.0.0.0:<remote port>` and
  forward incoming connections back to `localhost:<local port>` on your workstation.

The panel's **Active Tunnels** list shows each tunnel's direction, both endpoints, which
session owns it, its status and how many connections it is currently carrying, with a
button to remove it. Tunnels belong to a session, so the list is a view across every
session you have open.

## File Manager

A two-pane transfer window: your local filesystem on the left, the filesystem of a chosen
open session on the right. Drag files between the panes to upload or download, and use
the host picker above the remote pane to retarget it at a different session. SSH, FTP and
S3 sessions all qualify, and the remote pane works identically for each.

The **History** drawer records completed uploads and downloads for 15 days, from anywhere
in the application rather than just this window, and it survives restarts. You can filter
it by host, by direction and by free-text search. [Files and
transfers](files-and-transfers.md) covers this in more detail.

## Snippets

Command Snippets are saved command templates: a name, optional tags, an optional one-line
description and the command itself. Search by name, tag or command text, or filter by tag
from the dropdown. Two dozen snippets ship as a starting point, covering system
inspection, networking, logs, Kubernetes, Docker, OpenStack, Ceph and a handful of
security checks. Several contain deliberate `<placeholder>` text you are expected to edit
before running.

**Insert into terminal** types the snippet into the active session and leaves it at the
prompt. It does not press Enter for you — you read it and run it, the same way a proposal
from the assistant works.

Snippets are stored locally on the workstation, in the application's own storage, and are
not scoped to a connection or a group.

## System Monitor

The monitor polls the connected host every four seconds over a separate exec channel —
it does not type into your terminal — and shows the result in a bar above the workspace,
with a pause button and a close button. Reopening it is a single click in the status bar.

What it collects per poll:

- CPU utilisation, as a total and per core
- Memory: used, total, available, cached and buffers
- Disk size and usage for every mounted filesystem, with paging when there are many
- Network throughput in and out, plus a cumulative total since the session opened
- Host uptime, the logged-in user count, the hostname, and the OS name from
  `/etc/os-release`

It keeps about five minutes of CPU and memory history for the sparklines. The figures come
from `/proc/stat`, `/proc/meminfo`, `/proc/net/dev`, `/proc/uptime`, `df`, `who` and
`hostname`, so the monitor expects a Linux-shaped host; on anything else the fields it
cannot read stay empty.

## Insights

The Insights panel is a summary of the current run of the application rather than a
historical report. It shows:

- Cards for active sessions, commands sent, files edited and application uptime.
- **Active Sessions** — every open session with its user and host, port, environment tag,
  connected state and how long it has been up.
- **System Resources** — bars built from the most recent System Monitor sample, labelled
  with the host it came from, shown only when the monitor has data.

The counters reset when you restart OpsPilot. Nothing here is uploaded anywhere; there is
no telemetry behind it.

## Themes

Theming is covered in [The terminal](terminal.md#theming). One palette ships — the
NubeStack OpsPilot Infra Theme — and it drives the application chrome, the terminal's
ANSI colours, the editor theme and the colourisation of otherwise-plain SSH output from a
single definition.

## Search

**Ctrl+F** with the terminal focused opens the find bar for the session's scrollback, with
case-sensitive, whole-word and regular-expression toggles. See
[Search within scrollback](terminal.md#search-within-scrollback).

## Local utilities

Beyond the seven above, a **Tools** panel collects small local utilities that would
otherwise send you to a website: a password generator with an entropy estimate, a
Base64/URL/hex encoder and decoder, a JSON formatter with minify and sort-keys, an SSH
exec box that runs a command on the active session through a separate channel without
touching the interactive terminal, and a ping that runs from the connected server rather
than from your workstation.

The password generator uses the platform's cryptographic random source. The encoder and
the JSON formatter run entirely in the application and send nothing anywhere.

## See also

- [The terminal](terminal.md) — search, copy and paste, theming and terminal settings
- [Files and transfers](files-and-transfers.md) — the file explorer, the editor and
  transfer history
- [Sessions and tabs](sessions.md) — which session a tool acts on
- [Keyboard shortcuts](../reference/keyboard.md) — how to reach these without the mouse
