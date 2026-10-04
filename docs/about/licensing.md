# Privacy & licensing

What OpsPilot does with your data, how it is licensed, and the terms under which you use
it. A reviewer signing OpsPilot off will want the
[security model](../safety/security-model.md) alongside it.

## Privacy

**OpsPilot collects no telemetry.** There is no usage reporting, no crash beaconing and
no analytics.

**Your data goes to the AI provider you configured, and nowhere else.** Terminal output
reaches a model only after passing through the local redactor, on a single code path,
and only from sessions where you have enabled AI. Terminal control sequences are
removed first, and license keys are among the secrets redacted.

**Your configuration stays on the workstation.** Connections, groups, environments,
Command Safety profiles, Data Handling profiles and settings are local, with no sign-in
and no sync service in OpsPilot. SSH credentials and the tunnel runtime key are
encrypted via the operating system's credential store. Terminal scrollback is held in
memory only and is never written to disk by OpsPilot. The license state is stored
locally too: an online activation keeps an activation secret, encrypted by the operating
system, and OpsPilot never stores the license key itself.

### Licensing traffic

OpsPilot contacts NubeStack only if you activate a license online. It then contacts
`license.nubestack.com`:

- **once at activation**, sending the license key, a one-way device fingerprint, the
  device label you chose, the platform, the app version, and whether the key was set by
  IT in `policy.json`
- **every few minutes while it is running and the computer is in use**, to confirm this
  computer is still licensed. This check carries the device fingerprint and the app
  version, and the license server does not record it.
- **about once a day**, to renew its license. The renewal carries the same, and records
  when this device last renewed, which your organisation's license managers see in the
  portal.

OpsPilot never sends terminal content, host names, user names or anything about your
work to the license server. The trial, offline activation and deployment licenses make
no licensing connection at all, and IT can forbid licensing traffic by policy. With a
local model and offline activation or a deployment license, nothing leaves the machine.

The NubeStack subscription site keeps what you give it when you buy and manage
licenses: account email addresses, the names and emails you assign to seats, and each
activated device's label, device code, platform and app version. Payments are handled
by Paddle, NubeStack's reseller.

!!! note "Private or VPN-only is not air-gapped"
    With a cloud provider your *workstation* still reaches the internet, even though
    your targets do not. That is private or VPN-only operation. A genuinely air-gapped
    deployment needs a local or self-hosted model, and offline activation or a
    deployment license.

For the detail of what is included in an AI request and what is stripped first, see
[What gets sent to the AI](../ai/what-gets-sent.md).

## Licensing

OpsPilot is **proprietary software of NubeStack**, licensed per user and not sold. Each
user gets a license key that works on up to two devices. Licenses are signed by
NubeStack and checked on your computer with NubeStack's public keys, which are built
into OpsPilot, so they work without a network connection, and a fake or unreachable
server can neither forge a license nor switch one off.

- **Online:** enter the key in **Settings → License**. While OpsPilot is running and the
  computer is in use, it asks the license server every few minutes whether this
  computer is still licensed, and renews its license about once a day. A computer
  released in the portal stops using that license within minutes.
- **Offline:** OpsPilot shows a short request code. You, or whoever manages licenses in
  your organisation, enter it in the NubeStack portal and get a license file to import.
- **Deployment license:** for networks where nothing may leave, NubeStack can approve one
  file for the whole organisation, deployed by IT.

The details are in [Activate OpsPilot](../licensing/activation.md), and the price and
trial in [Plans & subscription](plans.md).

The end-user license agreement, the commercial license terms and the third-party notices
apply to your subscription. For a copy for a review, email support@nubestack.com.

## Legal notice

**NubeStack OpsPilot is the property of NubeStack.**

This product, its documentation, its interface, its name and its associated marks are
owned by NubeStack. OpsPilot is proprietary software, licensed and not sold. No part of
this product or this documentation may be copied, redistributed, reverse engineered or
used to create a derivative work without the prior written permission of NubeStack.

Copyright © NubeStack. All rights reserved.

## Third-party components

OpsPilot bundles third-party software, and each component is covered by its own
license. The **third-party notices supplied with the product are authoritative** —
consult them for the terms that apply.

The significant bundled components are Electron, xterm.js, Monaco Editor, noVNC, ssh2,
node-pty, the AWS SDK for S3 and the Model Context Protocol SDK, plus FreeRDP in the
Windows build only. For their license identifiers and notice text, read the third-party
notices that ship with your installation.

On macOS, RDP connections are handed to Microsoft's Windows App, which OpsPilot does
not bundle — you install it yourself, and it carries Microsoft's own terms.

## See also

- [Security model](../safety/security-model.md) — the threat model behind the privacy
  claims
- [What gets sent to the AI](../ai/what-gets-sent.md) — the exact egress path
- [Licensing for IT](../licensing/for-it.md) — turning licensing traffic off by policy
- [Hardening checklist](../safety/hardening.md) — what to set before a production
  rollout
