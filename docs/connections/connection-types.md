# Connection types

Ten connection types ship. The type you pick decides which fields the new
connection dialog shows, and what kind of surface the connection opens: an
in-app terminal tab, an embedded desktop, a two-pane file view, or an external
application launched in its own window.

| Type | Transport | Opens | Use it for |
|---|---|---|---|
| **SSH** | Encrypted, port 22 | In-app terminal | Linux, Unix, macOS servers. The primary path. |
| **Telnet** | **Unencrypted**, port 23 | In-app terminal | Legacy network gear with no SSH |
| **RSH** | **Unencrypted**, port 514 | In-app terminal | Legacy UNIX systems |
| **Mosh** | UDP roaming | Its own window | High-latency or unstable links |
| **RDP** | Encrypted, port 3389 | Embedded tab on Windows, its own window on macOS and Linux | Windows servers and desktops |
| **VNC** | Varies, port 5900 | Your VNC viewer in its own window, or an embedded console through a hypervisor | Linux desktops, KVM and OpenStack instance consoles |
| **FTP** | Varies, port 21 | Two-pane file view | File transfer |
| **AWS S3** | HTTPS | Two-pane object view | Buckets, artifacts, log archives |
| **Serial** | Local COM port | In-app terminal | Console access to appliances over a local COM port |
| **Local** | None | In-app terminal | Your own shell |

AI works in SSH and Local tabs only. During the free trial and without a
subscription, every type counts toward the 10 sessions you can have open at
once, including the programs OpsPilot starts in their own window. See
[Free trial and limits](../licensing/trial-and-limits.md).

!!! warning "Telnet and RSH are unencrypted"
    Credentials and session content travel in clear text, by the design of those
    protocols; there is no encryption to switch on. Both types exist because the
    switches, PDUs and old UNIX boxes that still require them are real. Use them
    on trusted management networks and nowhere else.

## SSH

> Secure Shell — encrypted terminal to Linux/Unix/macOS servers. Supports
> password or private key auth. The standard for server management.

Default port 22. Opens an in-app terminal tab with full xterm emulation. Shows
host, port, username and the password / ssh key choice. SSH sessions are the ones
that get the file explorer over SFTP, port forwarding and the system monitor.
SSH and Local are the two types that can use AI, and the two that can carry
their own Command Safety and Data Handling profiles.

## Telnet

> Telnet — unencrypted terminal over TCP (port 23). Use only on trusted networks
> or legacy network gear that lacks SSH support.

Default port 23. Opens an in-app terminal tab. Host and port only — no username
or password field, because Telnet devices prompt for credentials inside the
session itself.

## RSH

> Remote Shell — legacy unencrypted remote execution protocol (port 514). For old
> UNIX systems. Avoid on untrusted networks.

Port 514. Opens an in-app terminal tab. Shows host and username; there is no
password field and no port field in the dialog.

## Mosh

> Mobile Shell — SSH with roaming and local echo. Ideal for high-latency or
> unstable connections. Requires mosh installed on the server.

Default port 22, and the same host, port, username and password / ssh key fields
as SSH, because Mosh bootstraps over SSH. `mosh` must be installed on the server
as well as on your workstation.

Mosh **launches an external client** in its own window rather than opening an
in-app tab, so the AI panel and the in-app tooling do not apply to it. During
the free trial and without a subscription, a running Mosh session takes one of
the 10 places, and it shows in the tab bar as an entry such as
**Mosh · admin@db1**, with a **Close** button. See
[Sessions and tabs](../workspace/sessions.md).

## RDP

> Remote Desktop Protocol — Microsoft graphical desktop for Windows servers.

Default port 3389. Shows host, port, username, a single password field, an
optional Windows **domain**, and a **Force fresh session before connecting**
option. What you get differs by platform — an embedded in-app tab on Windows,
Microsoft's Windows App on macOS, and a FreeRDP client in its own window on
Linux. The per-platform detail, and what each platform needs installed, is on
[Remote desktop](remote-desktop.md).

## VNC

> Virtual Network Computing — cross-platform graphical remote desktop.

Default port 5900. VNC has a **target** selector, chosen before the other
fields, because it changes what they mean:

- **Direct VNC host** — host, port and a password, opening your system's own VNC
  viewer as an external application in its own window.
- **Hypervisor VM — KVM** or **Hypervisor VM — OpenStack** — the console of a
  virtual machine reached through its hypervisor, which opens as an embedded
  session inside OpsPilot. See [Hypervisor consoles](hypervisor-consoles.md).

## FTP / SFTP

> File Transfer Protocol — browse and manage files on an FTP server. Note:
> unencrypted. Use SFTP (via SSH) when possible.

Default port 21. Shows host, port, username, a password, an **initial path**, and
a **Secure (FTPS — explicit TLS)** option; with FTPS on, a further checkbox skips
TLS certificate verification for a self-signed or internal-CA certificate.

An FTP connection opens a tab with two panes instead of a terminal: your own
computer on the left (**Local**) and the FTP server on the right. Drag files
between them to upload or download. If FTP support is unavailable in your
installation, the connection reports that when you open it.

SFTP is not a separate connection type. It arrives through SSH: open an SSH
connection and the file explorer browses that host over SFTP, which is the
encrypted path the type description above is pointing you at. See
[Files & transfers](../workspace/files-and-transfers.md).

## AWS S3

> Amazon S3 / S3-compatible object storage. Browse buckets and objects on AWS S3,
> MinIO, Wasabi, Backblaze, and more.

No host and no port — S3 has its own field set instead. Opens the same two-pane
view as FTP, with the bucket on the right. If S3 support is unavailable in your
installation, the connection reports that when you open it. See
[Object storage](../workspace/object-storage.md).

## Serial

> Serial port — RS-232 console access to routers, switches, firewalls, and
> embedded devices via a COM port on your machine.

No host, no port and no credentials. You pick a **serial port** on your own
machine (the refresh button next to it reads the list again) and a **baud
rate**, 115200 unless you change it, and it opens an in-app terminal tab. This
is the path for an appliance you can only reach with a cable.

A Serial tab never has AI, so Serial connections show no AI badge in the side
list.

## Local

> A local shell on this machine, running as its own tab.

No host, no port, no credentials. Opens a real shell on your own workstation as
an in-app terminal tab. The **shell** field offers Command Prompt or PowerShell
on Windows, and zsh, bash or sh on macOS and Linux; **use default (Settings →
Terminal)** takes the shell set in **Settings → Terminal → Default shell**.

Every install ships with one ready-to-use **Local** connection, so there is
always a terminal available without filling in the dialog first. It starts with
AI off.

A Local tab can use AI in the same way as an SSH tab. The model is told that it
is working in a local shell on your own computer, which operating system and
shell it is, and that anything it proposes runs on your workstation. Its
proposals follow the same approval rules as an SSH session's. Give a Local
connection its own Command Safety profile if your Default profile is tuned for
disposable servers. See [The terminal](../workspace/terminal.md).

During the free trial and without a subscription, **Local** is not one of your
first 10 saved connections while it opens a local shell, so it is never locked.
If you change it into another type of connection, it counts like any other.

## See also

- [Add a connection](adding-connections.md) — creating any of these end to end
- [Remote desktop (RDP & VNC)](remote-desktop.md) — the graphical types in detail
- [Hypervisor consoles](hypervisor-consoles.md) — VNC through KVM or OpenStack
- [Connection fields reference](../reference/connection-fields.md) — every field,
  per type
