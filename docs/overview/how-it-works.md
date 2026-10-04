# How OpsPilot works

This page traces one complete turn — your question, the model's answer, the
command that runs — then sets out which of the two AI modes you are using and
where each kind of data is held. [Architecture](architecture.md) covers the
process separation and trust boundaries underneath it.

## The loop

1. **Connect.** OpsPilot opens an SSH, Telnet, RSH, Mosh, RDP, VNC, FTP, S3,
   serial or local session. Credentials come from the encrypted connection
   store. The AI loop below runs in SSH and local shell sessions; for a local
   shell the model is told which operating system and shell it is working in.
2. **Ask.** Type a question in the AI panel, or use one of the quick
   actions — **Analyze Error**, **Explain Command**, **Review Config**,
   **Optimize**.
3. **OpsPilot gathers context.** Recent scrollback for that session is
   collected. Only that session's own buffer is used.
4. **OpsPilot cleans and redacts.** Terminal control sequences are stripped
   first, so a secret broken up by a colour code still matches. The text then
   passes through the single redaction step, using the Data Handling profile
   resolved for that connection — connection, then group, then Default.
5. **The provider answers.** Only redacted text is sent. The reply is an
   explanation plus, usually, one proposed next command carrying the model's own
   assessment of it as safe or dangerous.
6. **OpsPilot classifies.** Your Command Safety profile is applied on top of
   that self-assessment. With the default **The AI's warning and my list**, a
   pattern match can raise the tier and nothing can lower it. With **Only my
   list**, your pattern list alone decides, and the model's warning is shown on
   the command as a note.
7. **Approve.** By default every command waits for your click. A profile can let
   <span class="tier tier-readonly">Read-only</span> commands, or everything
   except dangerous ones, run without a click. A
   <span class="tier tier-high">High risk</span> command always needs a click,
   and by default a typed reason first.
8. **The command runs.** Its output is cleaned and redacted as well, before it
   feeds into the next turn, so a multi-step investigation continues without you
   re-asking after every command.

Step 7 is the only way a proposal becomes a real keystroke on a real shell: either
your click, or a rule you set in advance in the profile. OpsPilot's own code types
the command in both cases, and the model never reaches the shell. That holds for
every AI path into OpsPilot, including an external assistant.

## The two AI modes

OpsPilot can either call a model you hold a key or sign-in for, or expose itself
to a chat application you already pay for. The two differ in cost, in whether
they can run offline, and in which application drives them. The safety behaviour
is identical.

| | AI Providers | AI Assistants |
|---|---|---|
| What it is | You supply an API key or sign-in | You connect an app you already pay for |
| Who drives | OpsPilot's own AI panel | Claude Desktop, ChatGPT, VS Code Copilot Chat |
| Cost model | Pay per request, or free with a local model | Model usage covered by your Claude, ChatGPT or Copilot plan |
| Offline capable | Yes, with Ollama or self-hosted | No — those apps are cloud services |
| Approval gate | Yes | Yes, identical |
| Redaction | Yes | Yes, same profiles |

Both modes need an active OpsPilot trial or subscription. Without one, the
terminal keeps working and AI is off; your providers, assistants and profiles stay
configured. See [Free trial and limits](../licensing/trial-and-limits.md).

Use [AI Providers](../ai/providers.md) when you want the AI inside OpsPilot, or
when you need a fully offline deployment. Use
[AI Assistants](../ai/assistants.md) when you would rather work in a chat
application you already have open, or drive the estate from your phone. Running
both at once is supported.

## Where things live

| Data | Where | Notes |
|---|---|---|
| SSH passwords and private keys | Connection store | Encrypted via the OS credential store |
| OpenAI tunnel runtime key | Its own file | Encrypted via the OS credential store, decrypted only into the tunnel process |
| Connections, groups, environments | Application settings | Local to this machine |
| Redaction and safety profiles | Application settings | Local to this machine |
| Terminal scrollback | Memory only | Written out only by the explicit **Save terminal output** or **Print terminal output** actions |
| License state (an online activation, imported license files) | The `licensing` folder in the application data folder | The activation secret is encrypted via the OS credential store; the license key itself is never stored |
| Trial start and clock records | The application data folder, plus small files under `NubeStack` in your user profile | Kept when you reinstall OpsPilot |
| Machine-wide license files and IT policy | `%ProgramData%\NubeStack\OpsPilot` on Windows | Placed by IT, writable only by administrators; see [Licensing for IT](../licensing/for-it.md) |

Everything in that table stays on your workstation. OpsPilot has no sign-in, no
sync service and no telemetry. The only connection it makes on its own is
optional: if you activate a license online, it contacts `license.nubestack.com`
— a quick check every 5 minutes while OpsPilot is running and the computer is in
use, and a renewal about once a day. Activating sends the license key once, with
the device label you choose and the platform; checks and renewals carry a device
fingerprint, the app version and a signed proof. None of them carries terminal
content, host names or user names. The trial, offline license files and
deployment licenses make no licensing connection at all.

## See also

- [Architecture](architecture.md) — the process separation and trust boundaries
  behind these steps
- [Execution boundary & risk tiers](../safety/risk-tiers.md) — steps 6 and 7 in
  full
- [What gets sent](../ai/what-gets-sent.md) — exactly what a provider receives
- [Activate OpsPilot](../licensing/activation.md) — online, offline or with a
  deployment license
