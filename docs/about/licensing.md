# Privacy & licensing

What OpsPilot does with your data, and the terms under which you use it. A reviewer
signing OpsPilot off will want the [security model](../safety/security-model.md)
alongside it.

## Privacy

**OpsPilot collects no telemetry.** There is no usage reporting, no crash beaconing and
no analytics.

**Your data goes to the AI provider you configured, and nowhere else.** Terminal output
reaches a model only after passing through the local redactor, on a single code path,
and only from sessions where you have enabled AI. With a local model, nothing leaves the
machine.

**There is no cloud backend, no account system and no sync service.** Connections,
groups, environments, Command Safety profiles, Data Handling profiles and settings stay
on the workstation. SSH credentials and the tunnel runtime key are encrypted via the
operating system's credential store. Terminal scrollback is held in memory only and is
never written to disk by OpsPilot.

There is therefore nothing to delete from a NubeStack server, because nothing was ever
sent to one. To close the loop completely, run a local model — see
[Offline AI with Ollama](../ai/offline-ollama.md).

!!! note "Private or VPN-only is not air-gapped"
    With a cloud provider your *workstation* still reaches the internet, even though
    your targets do not. That is private or VPN-only operation. Only a local or
    self-hosted model makes the deployment genuinely air-gapped.

For the detail of what is included in a request and what is stripped first, see
[What gets sent to the AI](../ai/what-gets-sent.md).

## Licensing

OpsPilot is **proprietary software of NubeStack**, licensed and not sold.

Commercial licence terms, the end-user licence agreement and third-party notices are
supplied with your subscription, and those documents are authoritative. Pricing and
trial terms are on [Plans & subscription](plans.md).

If you need a copy of the licence terms, the end-user licence agreement or the
third-party notices for a review, request them through the NubeStack support channel
supplied with your subscription.

## Legal notice

**NubeStack OpsPilot is the property of NubeStack.**

This product, its documentation, its interface, its name and its associated marks are
owned by NubeStack. OpsPilot is proprietary software, licensed and not sold. No part of
this product or this documentation may be copied, redistributed, reverse engineered or
used to create a derivative work without the prior written permission of NubeStack.

Copyright © NubeStack. All rights reserved.

## Third-party components

OpsPilot bundles third-party software, and each component is covered by its own licence.
The **third-party notices supplied with the product are authoritative** — consult them
for the terms that apply.

The significant bundled components are Electron, xterm.js, Monaco Editor, noVNC, ssh2,
node-pty, the AWS SDK for S3 and the Model Context Protocol SDK, plus FreeRDP in the
Windows build only. For their licence identifiers and notice text, read the third-party
notices that ship with your installation.

On macOS, RDP connections are handed to Microsoft's Windows App, which OpsPilot does
not bundle — you install it yourself, and it carries Microsoft's own terms.

## See also

- [Security model](../safety/security-model.md) — the threat model behind the privacy
  claims
- [What gets sent to the AI](../ai/what-gets-sent.md) — the exact egress path
- [Offline AI with Ollama](../ai/offline-ollama.md) — nothing leaves the machine
- [Hardening checklist](../safety/hardening.md) — what to set before a production
  rollout
