# Connection fields

Every field the new-connection and edit-connection dialogs can show, per connection
type. Which rows appear is decided by the type you pick at the top of the dialog, so
a field vanishing is the form adapting rather than something going wrong.

## Types, default ports and surfaces

| Type | Default port | Opens as |
|---|---|---|
| SSH | 22 | In-app terminal |
| Telnet | 23 | In-app terminal |
| RSH | 514, no port field | In-app terminal |
| Mosh | 22 | External application |
| RDP | 3389 | In-app tab on Windows, external client on macOS and Linux |
| VNC | 5900 | External viewer for a direct host, embedded desktop tab for a hypervisor target |
| FTP | 21 | File browser |
| AWS S3 | none | File browser |
| Serial | none | In-app terminal |
| Local | none | In-app terminal |

There is no SFTP connection type. SFTP is reached through the file explorer of an
SSH session, not as a connection of its own. FTP and AWS S3 are the two
file-transfer connection types.

For what each surface looks like in use, see
[Remote desktop](../connections/remote-desktop.md),
[Hypervisor consoles](../connections/hypervisor-consoles.md) and
[Network requirements](network-requirements.md).

## Field matrix

| Type | Host | Port | Username | Authentication |
|---|---|---|---|---|
| SSH | Yes | Yes | Yes | Password or SSH key (toggle) |
| Telnet | Yes | Yes | No | None — prompts on the device |
| RSH | Yes | No | Yes | None |
| Mosh | Yes | Yes | Yes | Password or SSH key (toggle) |
| RDP | Yes | Yes | Yes | Password only |
| VNC | Yes | Yes | No | Password only |
| FTP | Yes | Yes | Yes | Password only |
| AWS S3 | No | No | No | Own credential panel |
| Serial | No | No | No | None |
| Local | No | No | No | None |

A few properties of the rows themselves:

- **Host.** RSH gets a host field and no port field, even though its default port is
  514.
- **Port.** Where the field is shown, the default port is the field's placeholder
  rather than a pre-filled value, so any of them can be overridden per connection.
- **Authentication.** Choosing **ssh key** on the password / ssh key toggle swaps the
  password field for **private key file** (with a browse button) and **passphrase (if
  any)**.
- **No credentials.** Telnet and RSH still authenticate — on the device,
  interactively, after the session opens.

AWS S3 collects no host, port, username or password. Its credentials live in their
own panel instead, because S3-shaped credentials have nothing in common with a host /
port / username login.

## Fields common to every connection

| Field | Control | Notes |
|---|---|---|
| **connection name** | Text, required | The label used in the sidebar and tabs |
| **environment** | Select | Colour-coded tag. Hidden for AWS S3, Serial and Local |
| **group** | Select | Defaults to **— no group —**. Carries policy |
| **command safety profile** | Select | **— inherit from group —** by default. SSH only |
| **data handling profile** | Select | **— inherit from group —** by default. SSH only |
| **Enable AI** | Toggle | Sub-caption "AI panel for this host". Shown for SSH and Serial |
| **Save connection** | Toggle | Captioned "Credentials encrypted, never plaintext" |
| **Let AI Assistant open this session** | Toggle | Off by default. Also needs the master switch |

Only two environments seed on first run, `production` and `staging`, and those are
the two options the dialog offers. Add your own on **Settings → Environments**.

The dialog has no **Save** button. **Save connection** is a toggle, and the dialog is
committed with the **connect** button.

The two profile selects appear for SSH only, which is the same set of types that can
invoke AI at all; assigning a profile to a type that can never invoke AI would mean
nothing.

**Enable AI** is offered for SSH and Serial connections. External launchers, file
browsers, Telnet, RSH and Local do not show it: those sessions cannot reach the AI
panel, so the toggle would have been a setting that silently did nothing.

**Let AI Assistant open this session** is a second, stricter permission than
**Enable AI**. It is off for every new connection, and it only takes effect when
**Allow opening/reconnecting sessions** is also on in **Settings → AI Assistants →
Session access**. See [MCP tools](mcp-tools.md).

## Type-specific panels

### RDP

| Field | Control | Notes |
|---|---|---|
| **domain (optional)** | Text | Windows domain, e.g. `CORP` |
| **Force fresh session before connecting** | Checkbox, off by default | Windows only. Logs off any existing disconnected session for this user on the host first, over WinRM |

!!! warning "Force fresh session destroys unsaved work"
    The dialog says so itself: it destroys any unsaved work left open in that
    session. It also requires WinRM to be enabled and reachable on the target.

### VNC — target and hypervisor panels

VNC adds a **target** select directly under the connection name, because it changes
what the host, port, username and auth fields above it mean:

| Target | What the shared fields mean | Panel below |
|---|---|---|
| **Direct VNC host** | The VNC endpoint itself | None |
| **Hypervisor VM — KVM** | The KVM host's own SSH login | **List VMs**, then a searchable VM list |
| **Hypervisor VM — OpenStack** | Not used | A separate Keystone auth panel |

The KVM panel says this in the form itself: host, port, username and auth above are
the KVM host's own SSH login, used to list and reach its VMs, not entered separately.

The OpenStack panel is a fully separate field set:

| Field | Control |
|---|---|
| **auth URL (keystone)** | Text, e.g. `https://cloud.example.com:5000/v3` |
| Auth toggle | **password** or **app credential** |
| **username**, **user domain (name or ID)** | Text — password auth |
| **password** | Password — password auth |
| **project**, **project domain (name or ID)** | Text — password auth |
| **application credential ID** / **secret** | Text / password — app credential auth |
| **region (optional)** | Text, e.g. `RegionOne` |
| **Skip TLS certificate verification** | Checkbox, off by default |
| **List VMs** | Button, then a searchable VM list |

See [Hypervisor consoles](../connections/hypervisor-consoles.md).

### FTP

| Field | Control | Notes |
|---|---|---|
| **initial path** | Text, defaults to `/` | Where the browser opens |
| **Secure (FTPS — explicit TLS)** | Checkbox, off by default | |
| **Skip TLS certificate verification** | Checkbox | Only shown once **Secure** is checked |

!!! warning "Plain FTP is unencrypted"
    Without **Secure**, credentials and file contents travel in clear text. Prefer
    SFTP over an SSH connection.

### S3

| Field | Control | Notes |
|---|---|---|
| **region** | Text, e.g. `us-east-1` | |
| **bucket** | Text | |
| **access key ID** | Text | |
| **secret access key** | Password | |
| **custom endpoint (optional)** | Text, e.g. `https://s3.example.com` | For MinIO, Wasabi, Backblaze and other S3-compatible stores |

The secret access key takes the same encrypted credential path as any other saved
password. There is no initial-path field for S3 — an S3 connection always opens at
the bucket root.

### Serial

| Field | Control | Notes |
|---|---|---|
| **serial port** | Select, with a refresh button | Populated from the ports on this machine |
| **baud rate** | Select | 9600 to 921600. Defaults to 115200 |

### Local

| Field | Control | Notes |
|---|---|---|
| **shell** | Select | Defaults to **use default (Settings → Terminal)**. The other options are filled in per platform |

## See also

- [Connection types](../connections/connection-types.md) — what each type is for
- [Adding connections](../connections/adding-connections.md) — the dialog in order
- [Hypervisor consoles](../connections/hypervisor-consoles.md) — KVM and OpenStack
- [Network requirements](network-requirements.md) — the same ports, for a firewall review
