# Install

Installing OpsPilot is a single installer run on your own workstation. There is no
account to create, no cloud tenancy to provision and no licence server to reach. The
installers come from your NubeStack distribution channel rather than a public download
page, so get the file — and its published SHA-256 — from whoever distributes software
in your organisation.

The current product version is 0.1.0.

=== "Windows"

    1. Download `NubeStack-OpsPilot-Setup-0.1.0.exe` from your NubeStack distribution
       channel.
    2. Verify the published SHA-256 hash before running it.
    3. Run the installer. It installs per-machine, so you will get a UAC prompt, and
       you can choose the installation directory.
    4. Launch **NubeStack OpsPilot** from the Start menu.

    To check the hash in PowerShell before you run anything:

    ```powershell
    Get-FileHash .\NubeStack-OpsPilot-Setup-0.1.0.exe -Algorithm SHA256
    ```

    The installer is NSIS-based. It presents the directory choice rather than
    installing silently, and the per-machine scope is what raises the UAC prompt.

=== "macOS"

    1. Download the build that matches the machine. Both DMG and ZIP are available,
       for Intel (x64) and for Apple Silicon (arm64).
    2. Verify the published SHA-256 hash before opening the file.
    3. Open the DMG and drag **NubeStack OpsPilot** to **Applications**, or unpack the
       ZIP and move the application there.
    4. Launch it from **Applications**.

    Install Microsoft's Windows App if you need remote desktop: on macOS an RDP
    session opens there in its own window rather than as an in-app tab. Everything
    else works as it does on Windows.

=== "Linux"

    1. Download the AppImage from your NubeStack distribution channel.
    2. Verify the published SHA-256 hash before running it.
    3. Make it executable and run it. There is no package to install and no
       system-wide change.

    ```bash
    chmod +x <the-appimage-file>
    ./<the-appimage-file>
    ```

    Install FreeRDP (`xfreerdp` or `xfreerdp3`) on your `PATH` if you need remote
    desktop: on Linux an RDP session opens in an external window rather than as an
    in-app tab.

## What installation does not do

- **No account.** Nothing asks you to register, sign in or activate.
- **No cloud tenancy.** There is no backend to provision.
- **No licence server.** The application does not phone home to be allowed to start.
- **No telemetry.** Settings, connections and profiles stay on the workstation.
- **Nothing on your target hosts.** No agent, no daemon, no sidecar, no inbound
  firewall rule.

## What you have immediately

OpsPilot is fully functional as a terminal the moment it starts. You can create
connections, open sessions, transfer files and use the built-in tools without
configuring any AI at all.

The AI features activate once you either configure a provider under
**Settings → AI Providers** or connect an external assistant over MCP. Until then the
AI panel sits in its ready state and nothing is sent anywhere.

AI access is scoped per connection as well. A connection with **Enable AI** off is
invisible to every model and every assistant, so configuring a provider does not by
itself expose a session. Set the toggle deliberately on each connection you care
about rather than assuming a default.

## See also

- [System requirements](requirements.md) — check the workstation first
- [First run](first-run.md) — what you are looking at after the first launch
- [Quickstart](quickstart.md) — first connection to first AI answer
- [Add a connection](../connections/adding-connections.md) — the first thing to do
