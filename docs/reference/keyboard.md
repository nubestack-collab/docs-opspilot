# Keyboard shortcuts

The bindings the application listens for, grouped by where they apply. Where a
binding is scoped to a panel, the table says so. These tables are what the
application responds to. Bindings are fixed and are not configurable.

On macOS, Command works wherever Ctrl does — either modifier fires the same action,
and Control has not been disabled.

## Sessions and tabs

Handled globally, and suppressed while you are typing in a text field, except where
noted.

| Action | Windows & Linux | macOS |
|---|---|---|
| New connection dialog | ++ctrl+t++ | ++cmd+t++ |
| Close active tab (file tab first, else session) | ++ctrl+w++ | ++cmd+w++ |
| Close all tabs except the active one | ++ctrl+shift+w++ | ++cmd+shift+w++ |
| Close all inactive tabs | ++ctrl+shift+i++ | ++cmd+shift+i++ |
| Close all tabs | ++ctrl+shift+q++ | ++cmd+shift+q++ |
| Close all tabs to the left | ++ctrl+shift+left++ | ++cmd+shift+left++ |
| Close all tabs to the right | ++ctrl+shift+right++ | ++cmd+shift+right++ |
| Duplicate the active session | ++ctrl+d++ | ++cmd+d++ |
| Reconnect — only when the session is disconnected | ++ctrl+r++ | ++cmd+r++ |
| Next session tab | ++ctrl+tab++ | ++cmd+tab++ |
| Previous session tab | ++ctrl+shift+tab++ | ++cmd+shift+tab++ |
| Jump to session 1–9 by position | ++ctrl+1++ … ++ctrl+9++ | ++cmd+1++ … ++cmd+9++ |
| Pin or unpin the active tab | ++ctrl+shift+p++ | ++cmd+shift+p++ |
| Detach the active session into its own window | ++ctrl+shift+f++ | ++cmd+shift+f++ |
| Tab colour picker for the active session | ++ctrl+shift+k++ | ++cmd+shift+k++ |
| Copy the active session's hostname | ++ctrl+shift+h++ | ++cmd+shift+h++ |
| Rename the active session tab | ++f2++ | ++f2++ |

++ctrl+r++ only intercepts when the active session is disconnected. While a session
is live the key falls through to the shell, so bash reverse-i-search still works.

## Panels and layout

| Action | Windows & Linux | macOS |
|---|---|---|
| Toggle the AI panel | ++ctrl+backslash++ | ++cmd+backslash++ |
| Collapse or expand the terminal panel | ++ctrl+j++ | ++cmd+j++ |
| Toggle the left sidebar | ++ctrl+b++ | ++cmd+b++ |
| Open the file explorer panel | ++ctrl+shift+e++ | ++cmd+shift+e++ |
| Open the connections panel | ++ctrl+shift+c++ | ++cmd+shift+c++ |
| Focus the terminal | ++ctrl+grave++ | ++cmd+grave++ |
| Toggle fullscreen | ++f11++ | ++f11++ |

++f11++ is also registered as a system-wide shortcut, but only while the active
session is a connected RDP session — an embedded RDP window holds OS keyboard focus,
so the page's own listener would never see the key. It is unregistered the moment
that stops being true.

## Terminal

| Action | Windows & Linux | macOS | Scope |
|---|---|---|---|
| Find in the terminal | ++ctrl+f++ | ++cmd+f++ | Terminal focused, or the search bar already open |
| Next match | ++enter++ | ++enter++ | Search bar |
| Previous match | ++shift+enter++ or ++up++ | ++shift+enter++ or ++up++ | Search bar |
| Close the search bar | ++escape++ | ++escape++ | Search bar |
| Save terminal output to a file | ++ctrl+s++ | ++cmd+s++ | No editor focused |
| Print terminal output | ++ctrl+p++ | ++cmd+p++ | No editor focused |
| Increase terminal font size | ++ctrl+equal++ | ++cmd+equal++ | — |
| Decrease terminal font size | ++ctrl+minus++ | ++cmd+minus++ | — |
| Reset terminal font size | ++ctrl+0++ | ++cmd+0++ | — |
| AI: analyse the terminal | ++ctrl+shift+a++ | ++cmd+shift+a++ | — |
| Paste the clipboard into a VNC guest | ++ctrl+shift+v++ | ++cmd+shift+v++ | VNC session only |

The three zoom keys are intercepted before the page sees them, so that the browser
engine's own page zoom does not fire as well. ++ctrl+plus++ is treated the same as
++ctrl+equal++.

!!! note "The search bar's three options are buttons"
    Case sensitive, whole word and regular expression are toggle buttons in the find
    bar, clicked rather than keyed. The keys the bar itself handles are the ones in
    the table above: Enter, Shift+Enter, Up and Escape, plus Ctrl/Cmd+F to open it.

## File explorer

Active only while a file explorer pane has focus, and suppressed while an inline
prompt is open.

| Action | Windows & Linux | macOS |
|---|---|---|
| Refresh | ++f5++ | ++f5++ |
| Delete the selection | ++delete++ | ++delete++ |
| Go to the parent directory | ++backspace++ | ++backspace++ |
| Copy the selection | ++ctrl+c++ | ++cmd+c++ |
| Cut the selection | ++ctrl+x++ | ++cmd+x++ |
| Paste into the current directory | ++ctrl+v++ | ++cmd+v++ |

## File editor

Registered on the Monaco editor, so they apply only while the editor has focus.
Monaco's own default bindings are in effect alongside these.

| Action | Windows & Linux | macOS |
|---|---|---|
| Save the file | ++ctrl+s++ | ++cmd+s++ |
| Toggle word wrap | ++alt+z++ | ++alt+z++ |
| Format the document | ++alt+shift+f++ | ++alt+shift+f++ |
| Go to line | ++ctrl+g++ | ++cmd+g++ |
| Editor command palette | ++ctrl+shift+p++ | ++cmd+shift+p++ |

++alt+z++ also works when the editor does not have focus, as a fallback, provided a
file is open. ++ctrl+shift+p++ means the editor's command palette while the editor is
focused, and pin/unpin tab otherwise — the global handler stops at any focused text
field, and the editor's input is one.

## Dialogs

| Action | Key |
|---|---|
| Close the settings overlay | ++escape++ |
| Cancel an inline prompt or rename | ++escape++ |
| Confirm an inline prompt or rename | ++enter++ |

## See also

- [The terminal](../workspace/terminal.md) — search, output and font controls
- [Sessions & tabs](../workspace/sessions.md) — what the tab shortcuts operate on
- [Files & transfers](../workspace/files-and-transfers.md) — the explorer and editor
- [Settings map](settings-map.md) — the settings these shortcuts do not replace
