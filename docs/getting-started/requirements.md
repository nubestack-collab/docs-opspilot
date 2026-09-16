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
| Network | Reachability to your targets | Plus outbound HTTPS to your AI provider, unless running local inference |

OpsPilot needs to reach the hosts you want to operate, the way your existing tooling
does. It does not need the hosts to reach anything. Outbound HTTPS to an AI provider is
required only if you are using a cloud provider — with local inference, or with no AI
configured at all, it is not.

## Platform support

Windows is the primary supported platform. macOS (Intel and Apple Silicon) and Linux
(AppImage) builds are produced from the same codebase.

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

VNC, including the KVM and OpenStack hypervisor consoles, is embedded in-app on all
three platforms and needs no external client.

## Fully offline operation

"Fully offline" means the AI model runs on your workstation too, so no terminal
content leaves the machine at all. That is a property of your model choice rather than
a setting in OpsPilot, and it changes the hardware you need:

- A small Ollama model such as `llama3.3` or `phi4` runs acceptably on 16 GB of RAM.
- Larger models want a GPU.

Those figures are for the model, on top of OpsPilot's own requirements — OpsPilot does
not become heavier when you point it at a local endpoint. Small local models are less
precise at diagnosis than the large cloud models, so test the model you intend to use
against your own estate before committing to it.

!!! note "Private or VPN-only is not air-gapped"
    With a cloud provider, your workstation still reaches the internet. That is
    private or VPN-only operation, and it is what most regulated teams need. Genuine
    air-gapped operation requires a local model.

## See also

- [Install](install.md) — installers and verification, per platform
- [Offline with Ollama](../ai/offline-ollama.md) — setting up local inference
- [Network requirements](../reference/network-requirements.md) — the ports and
  destinations to open, per provider
- [Remote desktop (RDP & VNC)](../connections/remote-desktop.md) — the embedded and
  external paths in detail
