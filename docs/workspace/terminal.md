# The terminal

The terminal in the workbench is a full xterm-compatible terminal, so full-screen curses
applications, colour, mouse reporting and progress output all behave the way they do in
any other terminal. This page covers what it can do and which of its behaviours you can
change.

## Emulation and rendering

Rendering is GPU-accelerated. If the machine has no usable GPU context, or the context is
lost later — after a driver reset, for instance — the terminal falls back to software
rendering rather than failing. You get slower rendering, not a dead terminal.

The rest of the emulation follows from that:

- The terminal grid stays sized to its panel, so resizing the window or dragging the
  terminal's resize handle re-flows the session.
- URLs in output are clickable, and open in your default browser.
- Search runs over the session's scrollback, described below.
- A session detached into its own window keeps its scrollback, and keeps it again when
  you redock it.

## The local shell

A **Local** connection opens a real shell on your own machine as its own tab. On Windows
that is `cmd.exe` or PowerShell; on macOS and Linux it is `$SHELL`, falling back to zsh
or bash. **Settings → Terminal → Default shell** sets which one is used when a Local
connection does not specify its own.

This is an ordinary terminal tab, not a limited console — the same emulation, search,
copy and paste and theming as a remote session.

## Search within scrollback

**Ctrl+F**, with the terminal focused, opens the find bar at the top of the terminal
panel. Typing searches as you go and highlights matches. The bar has three toggles —
case sensitive (**Aa**), whole word (**W**) and regular expression (**.\***) — and the
arrows, or **Enter** and **Shift+Enter**, move between matches. **Escape** closes it and
returns focus to the terminal.

Search runs over the scrollback buffer held in memory for that session. It is not an
index, and it does not reach output that has already scrolled out of the buffer — see
Scrollback below for the buffer size.

## Copy and paste

Two settings under **Settings → Terminal → Interaction** govern this, and both ship on:

- **Copy on select** — releasing the mouse after a drag-selection copies the selection to
  the clipboard.
- **Right-click pastes clipboard** — a plain right-click pastes, when nothing is
  selected.

Pasting more than one line raises a confirmation first, showing the lines you are about
to send, because an interactive shell can run each line the moment it lands. The prompt
carries a do-not-show-again option. Single-line pastes go straight through.

## The analysis context menu

**Ctrl+right-click** (or **Cmd+right-click**) on selected terminal text opens an analysis
menu instead of pasting. It inspects what you selected, labels what it thinks it is, and
offers actions that fit.

It recognises IPv4 and CIDR notation, IPv6, port numbers, URLs, filesystem paths, JSON,
base64, PEM certificate blocks, MD5/SHA hashes, UUIDs, email addresses and version
strings. Depending on the type you get things like a CIDR calculator showing network,
netmask, broadcast and usable range; a port-to-service lookup; JSON pretty-printing and
minification; base64 decoding; certificate parsing on the server; and openers that jump
the selected path into the file explorer or the editor.

Every entry is either a local calculation or a command typed into your terminal. Nothing
in this menu is an AI action, and nothing here bypasses the approval gate for commands
the assistant proposed.

## Theming

One palette ships: the **NubeStack OpsPilot Infra Theme**. Applying it sets the
application's interface colours, the terminal's 16-colour ANSI palette and the editor's
theme from the same definition, which is why the terminal, the editor and the chrome
agree with each other. The theme also drives the colourisation applied to plain-text SSH
output, so infrastructure keywords, status words and destructive commands stand out in
output that arrived with no colour of its own.

## Settings that change terminal behaviour

All of these live in **Settings → Terminal**.

| Setting | Default | Notes |
|---|---|---|
| Font size | 13 px | Applies to the terminal and the editor |
| Line height | 1 | |
| Scrollback buffer | 10,000 lines | Range 500–100,000 |
| Cursor style | Bar | Block, bar or underline |
| Cursor blink | On | |
| Copy on select | On | |
| Right-click pastes clipboard | On | When no text is selected |
| Bell | None (silent) | Or visual flash, or sound |
| Minimum terminal height | 220 px | The panel is resizable above this |
| Default shell | Per platform | Used by Local connections |

Font size can also be changed from the tab context menu or with **Ctrl+=** and
**Ctrl+-**; that applies to every open terminal, not just the active one, and to the
editor when the editor has focus.

## Scrollback

The scrollback buffer lives in the session's memory for as long as its tab is open.
OpsPilot does not write it to a log file, a cache or a database, and it does not survive
closing the tab. The only exceptions are ones you ask for explicitly: **Save terminal
output** and **Print terminal output** on the tab context menu, which export the buffer
at that moment to a location you choose.

There is therefore no stored session history on the workstation to expire or protect, and
the assistant's view of a session is always built from the live buffer, passing through
the redactor on the way out. See [What gets sent](../ai/what-gets-sent.md).

## See also

- [Sessions and tabs](sessions.md) — tabs, auto-reconnect and detached windows
- [Built-in tools](tools.md) — search, themes and the other in-session tools
- [Keyboard shortcuts](../reference/keyboard.md) — the full list of bindings
- [Settings map](../reference/settings-map.md) — where each setting is stored
