# Release notes

Release history for NubeStack OpsPilot, newest first. The current version is **0.5.1**.
**Settings → About** shows the version you are running; include it when you
[contact support](support.md).

Installers are on <https://subscription.nubestack.com/download>. OpsPilot never updates
itself: download the new installer and run it. Settings, connections and the license
are kept.

## 0.5.1

In the free trial, AI is on for 2 of your connections at a time, and you choose which.

### Added

- The trial's 2 AI places belong to saved connections, not to open tabs. On the first
  start of 0.5.1 during the trial they go to your two oldest saved connections with AI
  turned on (Local only if you turned AI on for it; on a new install, the first two
  connections you save). Every other connection shows "AI off · trial limit", and its
  tabs open with AI off.
- To move AI, click a connection's "AI off · trial limit" badge or turn AI on in its
  tab: a free place is taken at once; otherwise a dialog names the two connections that
  have AI and offers "Use AI on … instead of …". The connection AI is taken from keeps
  its own AI setting and gets AI back when you subscribe.
- AI is on in at most 2 tabs. With one connection open twice, a tab of the other says
  "AI off · free trial: AI in 2 tabs at a time" (badge "AI off · tab limit"), and
  turning AI on there offers to move AI from one of those tabs.
- The side list works from the keyboard: it is one Tab stop, the arrows move between
  connections and groups, Right reaches a connection's AI badge, Enter or Space uses it,
  and Shift+F10 opens the connection's menu, which now offers **Turn AI on** or **Turn
  AI off**. Screen readers read the list as a tree, and each badge's name starts with
  the words it shows.

### Changed

- The side list shows what applies to every connection, open or not: "AI", "AI off", or
  why the license keeps AI off. The thin outline badge for a closed connection's own
  setting is gone. Serial connections, which never have AI, no longer show an AI badge.
- Turning AI off for one of the two connections, deleting it, or the connection becoming
  locked frees its place; nothing takes it by itself, not even a connection you add or
  duplicate afterwards. The places are kept when you restart OpsPilot.
- Choosing a connection in an AI dialog, or turning AI on in one of its tabs, turns AI
  on for that connection, so it keeps its place when its tabs close. Moving a connection
  to another group never changes its AI setting.
- An unsaved connection takes a free place while its tab is open, but not when it
  reconnects after waiting for one.
- During the trial, an AI Assistant can open only connections AI is on for, and not
  while AI is already on in 2 tabs of other connections; it is told why. An assistant
  that opens a connection gets its tab that has AI.
- A tab whose saved connection is deleted stays open and can have AI as a tab of its
  own. A tab whose connection becomes locked finishes an answer under way, then says "AI
  off · this saved connection is locked".
- A disconnected tab says what will keep AI off when it reconnects, follows the license
  (after you subscribe it no longer shows the trial's words), and offers no AI buttons
  until it reconnects.
- A badge that only says why the license keeps AI off does nothing when clicked.

### Fixed

- If a place was freed or taken while an AI dialog was open, choosing in it never takes
  AI from a connection you did not choose: OpsPilot asks again with the dialog that
  fits.
- When AI is on in 2 tabs of one connection, the connections dialog offers only that
  connection's place and says why, instead of offering a choice that cannot work.

### Known limitations

- The download page offers the Windows x64 installer and, for Linux x64, a `.deb`
  package and an AppImage. macOS installers are not published yet; email
  support@nubestack.com to hear when they are.
- The Linux packages are not code-signed; check their SHA-256 against the download page.
- The Windows installer is signed with NubeStack's own code-signing certificate, which
  Windows does not trust by default, so it shows **Unknown publisher**.

## 0.5.0

What the free trial and no subscription allow, made clearer and harder to get around.

### Added

- Without a subscription (the free trial, after it, or a subscription or license file
  that ran out) OpsPilot uses your first 10 saved connections, the ones you created
  first. The others stay in the list with a lock and are kept; deleting one of the
  first 10 unlocks the next, and subscribing unlocks them all. A clock problem, an
  identity check or another problem with a paid license locks none.
- Mosh, VNC viewers and RDP on macOS and Linux, which run in their own window, count
  toward the 10 sessions open at once while they run, and show in the tab bar with a
  **Close** button.
- The side list shows each open connection's AI state: AI, AI off · trial limit, AI off
  · no subscription, AI off · subscription ended, AI off · license expired, AI paused or
  AI off · licensing problem.

### Changed

- In the free trial, the first two sessions that want AI get it. Turning AI on in a
  third opens a dialog that names the two and offers "Use AI here instead of …";
  nothing turns on by itself. (0.5.1 moves the trial's AI to connections you choose.)
- After subscribing or renewing, AI comes back by itself in every open session that
  wants it, except where you turned it off, including a session AI was moved away from
  and one where turning AI on was refused or the dialog was cancelled. "Move AI here"
  and "Turn AI on here" after a renewal are gone.

## 0.4.1

Tighter licensing checks; nothing changes in normal use.

### Fixed

- Setting the computer's clock back no longer stretches a free trial or a license that
  has ended. OpsPilot accepts one small correction, of up to 48 hours, in total.
- Release builds no longer let a debugger attach to the app, and detached terminal and
  remote desktop windows no longer offer developer tools.
- An open session can no longer be reopened as a different kind of connection to fit
  more connections into the session limit.

## 0.4.0

Clearer notices when a computer is released, and a code to tell computers apart.

### Added

- When a computer activated online is released, OpsPilot says who released it and
  whether the old key still works: it was released from its subscription, the seat got
  a new key, NubeStack support released it, or it was released after 45 days without
  renewal. The notice appears below the tabs without taking the keyboard from the
  terminal.
- Each computer has a **device code**, such as `R59-EFG`, shown in **Settings →
  License** and next to the same computer in the NubeStack portal. When a seat is full,
  the devices holding it are listed with their codes.

### Changed

- A computer whose license key is set by IT in `policy.json` no longer registers itself
  again with that key after your organisation releases it; IT changes the key, or
  someone activates by hand. A release after 45 days without renewal still registers
  again by itself.

### Fixed

- **Settings → License** no longer shows a release message twice.

## 0.3.0

Licensing that follows the portal within minutes.

### Added

- A computer activated online asks the license server every few minutes while OpsPilot
  is running and the computer is in use, and at start, on wake and on screen unlock. A
  device released in the portal stops using that license within about 5 minutes if
  OpsPilot is open and online there. A settled payment or a lifted hold restores the
  license just as quickly. The check sends nothing about your work, and the license
  server does not record it.
- Trials, offline license files and deployment licenses still make no network requests
  at all.

### Changed

- An AI answer already on its way when the license ends finishes instead of stopping
  mid-sentence; new AI requests are refused, nothing that answer proposes runs by
  itself, and no session is closed.
- **Settings → License** is laid out in steps: **1 Get a subscription**, **2 Activate
  this computer** (online, offline, or import a license file), and **Your license** once
  the computer is licensed. The "Last checked" and "Next check" times are gone; a real
  problem still shows as a warning.

## 0.2.0

Subscriptions and licensing, Command Safety per profile, and AI on local shells.

### Added

- A 15-day free trial on first launch, with no account or email: up to 10 open
  sessions, AI on 2 of them. (0.5.0 adds the first 10 saved connections and counts
  programs in their own window; from 0.5.1 AI is on for 2 connections you choose.)
- After the trial, without a subscription, the terminal keeps working with up to 10
  sessions; AI, AI Assistants and the ChatGPT tunnel are off, and all settings are kept.
- **Settings → License:** activate online with a license key, or offline with a request
  code and a license file, with no network needed on the computer.
- Organisation-wide deployment licenses, and an IT policy file (`policy.json`) in a
  machine-wide folder, which the Windows installer makes writable by administrators
  only.
- Clock repair: a file for one computer, or one file for every computer on a deployment
  license.
- A new redaction category, **NubeStack license keys**, on by default, so a license key
  shown in a terminal never reaches an AI provider. There are now 11 built-in
  categories.
- AI on **Local Console** tabs on Windows, macOS and Linux. The model is told which
  operating system and shell it is working in.
- Command Safety is set per profile, with three settings: **Run commands without asking
  me** (**Ask me every time**, **Only read-only commands** or **Everything except
  dangerous ones**), **What counts as a dangerous command** (**The AI's warning and my
  list** or **Only my list**), and **Make me type a reason for dangerous commands**.
  A summary restates the rules in plain sentences, and saving a looser combination asks
  you to confirm it. Dangerous commands always need a click.
- The AI panel's chip shows **Auto-run off**, **Auto-run read-only** or **Auto-run all**
  for the active session.
- OpsPilot runs once per user, so a second copy cannot double the limits.

### Changed

- The old workstation-wide auto-run switch moves into each profile. If it was on, each
  profile gets **Only read-only commands**, so nothing runs unattended that did not
  before.
- Terminal control sequences are removed from terminal output before redaction. A
  secret split by a control sequence is now redacted, and shell-integration markers
  that carry the host's machine ID and host name no longer reach the AI provider.
- The Windows installer is signed by NubeStack.

### Fixed

- Session counting: Telnet, RSH and serial tabs, SSH `exit`, reconnects, window reloads
  and detached windows.
- Claude Desktop and VS Code now get OpsPilot's tools even when they start before
  OpsPilot, and **Settings → AI Assistants** says **Needs update** when an assistant is
  set up for another copy of OpsPilot or an old token.

## 0.1.0

First packaged release of NubeStack OpsPilot.

### Added

- Multi-session terminal workspace across ten connection types.
- Ten AI providers, including local Ollama and any OpenAI-compatible endpoint.
- MCP connector for Claude Desktop, VS Code Copilot Chat, ChatGPT Desktop and ChatGPT
  Web/Work.
- Secure tunnel for browser and mobile ChatGPT access.
- Approval-gated command execution with three risk tiers.
- Command Safety profiles, assignable per connection and per group.
- Data Handling redaction profiles with ten built-in categories and validated custom
  patterns.
- Encrypted credential storage for SSH and the tunnel runtime key.
- Embedded RDP and VNC, SFTP/FTP file management, S3 browsing.
- Port forwarding, snippets, system monitor, Monaco-based file editing.
- Idle lock and session lock policy.

### Known limitations

- RDP certificate handling is provisional ahead of GA; see
  [Remote desktop](../connections/remote-desktop.md).
- RDP is an embedded in-app tab on Windows and an external client window on macOS and
  Linux — Microsoft's Windows App on macOS, FreeRDP on Linux.
- Telnet and RSH are unencrypted by protocol design, so credentials and session content
  travel in clear text.
- Approvals and proposals are visible in a session while it is open, and terminal
  scrollback is memory-only, so this release writes no durable audit log; capture
  compliance evidence from your own session-recording arrangements.
- Remote operation through the tunnel covers triage — service status, logs, confirming
  an alert — and a High risk command still requires a typed justification at the
  workstation.

## See also

- [Plans & subscription](plans.md) — the trial, the price and what the subscription
  includes
- [Free trial and limits](../licensing/trial-and-limits.md) — the licensing rules in
  detail
- [Support](support.md) — what to include when you report a problem
- [Backup & upgrade](../operations/backup-and-upgrade.md) — before you move to a new
  version
