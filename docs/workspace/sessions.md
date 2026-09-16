# Sessions and tabs

A *connection* is a saved definition — a host, a port, credentials, an environment and an
AI setting. A *session* is one open instance of it. This page is about the open ones: how
they stack up as tabs, what happens when one drops, and how to move one out of the main
window.

## Session tabs

Double-clicking a connection opens a session in a tab on the strip along the top of the
window. You can run many at once, across different hosts and different connection types,
and switch between them either by clicking a tab or by using the session dropdown in the
terminal header — the control labelled with the current session name, which lists every
open session.

Right-clicking a tab gives you the per-session actions: pin the tab, detach it, take it
fullscreen, save or print its output, change the font size, disconnect or reconnect, copy
the hostname, and the various close-other-tabs operations. Each has a shortcut shown
beside it in the menu; [Keyboard shortcuts](../reference/keyboard.md) collects them.

The terminal header also carries a **Broadcast: send to all sessions** toggle, which
sends what you type to every open session rather than just the active one. Read the tab
strip before you turn it on.

## Auto-reconnect

**Auto-reconnect** re-establishes a session whose connection dropped. It is on by
default, and the status bar shows its state as **Auto: ON** or **Auto: OFF** — click it
to toggle.

When an SSH session that had successfully connected at least once drops, the terminal
prints a countdown and reconnects when it reaches zero. Pressing any key during the
countdown cancels it, after which you can reconnect by hand with **Enter** or **Ctrl+R**.
A session that never connected in the first place does not get a countdown; it tells you
how to retry instead.

!!! note "Auto-reconnect applies to SSH"
    Telnet, RSH and serial sessions print the disconnect and wait for you to reconnect
    them.

## Detached windows

A session can be **detached** into a window of its own — click the **Float terminal
(detach)** button in the terminal header, or choose **Detach tab** from the tab's context
menu. This is how you get a terminal onto a second monitor while the AI panel stays on
the first. The tab stays in the strip with a marker showing it lives elsewhere, and
clicking it brings the detached window to the front. Redocking returns the session, and
its scrollback, to the main window.

A detached window is a live terminal view of a session that is already connected. The
features that act across the workbench stay in the main window: broadcast mode, the idle
lock, the working-directory sync with the file explorer, the analysis context menu, and
saving or printing terminal output. Redock the session to use any of them.

## Working across many hosts

The session tab strip, the per-connection AI toggles and per-group policy are designed to
work together. A realistic production setup looks like this:

- A **Production** group with a strict Command Safety profile and a Data Handling
  profile that also scrubs hostnames and IP addresses.
- A **Staging** group with a moderate profile.
- A **Lab** group with a permissive profile and minimal redaction.

The same assistant behaves differently in each, without you changing a setting when you
switch tabs. Both profiles resolve from the connection, then its group, then the Default
profile. Auto-run is not part of either profile — it is one switch for the workstation —
see [Data Handling profiles](../safety/data-handling-profiles.md) and
[Organising connections](../connections/organising.md).

### Per-session AI context

Each session keeps its own AI conversation. The transcript in the AI panel belongs to the
session tab it was opened in and lasts as long as that tab does; switching tabs switches
the transcript rather than merging them. The provider choice is per session too, so you
can point a lab session at a small local model and a production session at a larger one.
**New AI session** clears the conversation for the current session only.

Sessions are kept separate. To have the assistant reason across two hosts, say so and
attach what it needs; OpsPilot does not pool the scrollback of unrelated sessions into
one context.

A connection whose AI badge is off contributes nothing at all — no scrollback, no files,
no presence. That control is per connection.

## See also

- [The terminal](terminal.md) — what the session's terminal itself can do
- [Organising connections](../connections/organising.md) — groups, environments and the
  per-connection AI badge
- [Using the AI panel](../ai/using-the-ai-panel.md) — asking questions about a session
- [Troubleshooting](../operations/troubleshooting.md) — when a session will not come back
