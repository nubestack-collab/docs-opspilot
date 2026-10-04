# System requirements

OpsPilot is a desktop application, so the requirements below describe the workstation
you install it on. Nothing is installed on the hosts you connect to. The table covers
OpsPilot itself; running a local model on the same machine adds its own requirements,
covered further down.

## Minimum and recommended

| | Minimum | Recommended |
|---|---|---|
| OS | Windows 10 22H2 / Windows 11 | Windows 11 |
| RAM | 4 GB | 8 GB or more |
| Disk | 600 MB | 1 GB |
| Display | 1280 × 800 | 1600 × 1000 or wider |
| Network | Reachability to your targets | Plus outbound HTTPS to your AI provider, unless running local inference. Optional: HTTPS to `license.nubestack.com`, only if you activate online |

OpsPilot needs to reach the hosts you want to operate, the way your existing tooling
does. It does not need the hosts to reach anything. Outbound HTTPS to an AI provider is
required only if you are using a cloud provider — with local inference, or with no AI
configured at all, it is not.

Licensing needs no network unless you choose online activation. The free trial,
offline activation with a license file and an organisation-wide deployment license all
work with no connection at all. See
[Network requirements](../reference/network-requirements.md) for the licensing
connection in detail.

## Platform support

Windows is the primary supported platform. The
[download page](https://subscription.nubestack.com/download) has the Windows x64
installer and, for Linux x64, a `.deb` package and an AppImage. macOS (Intel and Apple
Silicon) builds are produced from the same codebase, but the macOS installers are not on
the download page yet; email support@nubestack.com to hear when they are.

SSH, Telnet, RSH, Mosh, FTP, S3, hypervisor VNC consoles, the MCP connector and the AI
features behave identically on all three. The one difference is remote desktop: on
Windows an RDP session is embedded as an in-app tab, while on macOS and Linux it opens
in an external client. The window-embedding interface the Windows behaviour depends on
has no counterpart on macOS, and Wayland blocks cross-application embedding outright.

## Remote desktop

RDP needs a client, and which one depends on the platform:

| Platform | RDP client | You need to install |
|---|---|---|
| Windows | Bundled FreeRDP, embedded as an in-app tab | Nothing |
| macOS | Microsoft's Windows App, launched with a generated `.rdp` file | Windows App |
| Linux | FreeRDP (`xfreerdp` or `xfreerdp3`), external window | `xfreerdp` on your `PATH` |

Only the Windows build bundles a FreeRDP binary, so "nothing extra to install" applies
to Windows alone. On macOS, OpsPilot does not write the password into the generated
`.rdp` file, so Windows App prompts for credentials.

The KVM and OpenStack hypervisor consoles are embedded in-app on all three platforms
and need no external client. A direct VNC host opens in your system's VNC viewer.

## Licensing

- **The free trial** needs OpsPilot to be able to save its settings in your user
  profile, where it keeps the trial's start. On a locked-down desktop where it cannot,
  no trial starts: the terminal still works with up to 10 sessions open at once, and
  **Settings → License** says why. For evaluations on such machines, ask NubeStack
  (support@nubestack.com) for an evaluation license.
- **The computer's date and time** should be correct. If the clock is wrong on first
  launch, the trial waits until it is corrected, so you still get all 15 days.
- **Online activation** keeps a per-device secret in the operating system's secure
  storage. Windows and macOS always have it; on Linux it needs an unlocked keyring such
  as GNOME Keyring or KWallet. Without one, use offline activation instead.

## Fully offline operation

"Fully offline" means the AI model runs on your workstation too, so no terminal
content leaves the machine at all. That is a property of your model choice rather than
a setting in OpsPilot, and it changes the hardware you need:

- A small Ollama model such as `llama3.2` (3B) runs on a 16 GB workstation without a GPU.
- Larger models want a GPU and much more memory: `llama3.3`, the one OpsPilot's own
  setup text suggests, is a 70B model.

Those figures are for the model, on top of OpsPilot's own requirements — OpsPilot does
not become heavier when you point it at a local endpoint. Small local models are less
precise at diagnosis than the large cloud models, so test the model you intend to use
against your own estate before committing to it.

!!! note "Private or VPN-only is not air-gapped"
    With a cloud provider, your workstation still reaches the internet. That is
    private or VPN-only operation, and it is what most regulated teams need. Genuine
    air-gapped operation requires a local model, and an offline license file or an
    organisation-wide deployment license so that licensing needs no network either.

## See also

- [Install](install.md) — the download page, verification and setup
- [Offline with Ollama](../ai/offline-ollama.md) — setting up local inference
- [Network requirements](../reference/network-requirements.md) — the ports and
  destinations to open, per provider
- [Remote desktop (RDP & VNC)](../connections/remote-desktop.md) — the embedded and
  external paths in detail
