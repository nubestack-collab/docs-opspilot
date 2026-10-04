# Dangerous patterns

The default dangerous-command pattern list, in full, and how matching works.

## The default list

Twenty-one patterns, exactly as they ship:

```text
rm -rf
rm -fr
dd if=
mkfs
drop table
drop database
truncate
delete from
shutdown
reboot
init 0
init 6
kubectl delete namespace
kubectl delete pv
helm uninstall
iptables -f
chmod -r 777 /
chown -r
userdel
groupdel
:(){ :|:& };:
```

## Grouped by what they destroy

| What is at risk | Patterns |
|---|---|
| Filesystem | `rm -rf`, `rm -fr` |
| Disk and formatting | `dd if=`, `mkfs` |
| Database | `drop table`, `drop database`, `truncate`, `delete from` |
| Service and host availability | `shutdown`, `reboot`, `init 0`, `init 6`, `iptables -f` |
| Orchestration | `kubectl delete namespace`, `kubectl delete pv`, `helm uninstall` |
| Permissions and accounts | `chmod -r 777 /`, `chown -r`, `userdel`, `groupdel` |
| Fork bomb | `:(){ :|:& };:` |

`iptables -f` is `iptables --flush`, which can cut you off from the host you are
working on. `init 0` and `init 6` are halt and reboot on SysV-style systems.

## How matching works

**Plain-text, case-insensitive substring matching. Not regular expressions.** A
pattern matches when the command contains it, whatever the case. So `rm -rf` matches
`sudo rm -rf /var/tmp/x` and `RM -RF`. It does not match `rm -r -f`, and `chown -r`
matches any recursive `chown` anywhere in the command, including a harmless one.
Substring matching is blunt in both directions.

**A match can only add the dangerous classification.** If a pattern matches, the
command is dangerous and any safe marking from the model is cleared. Nothing in the
list, and nothing you can put in it, can demote a command.

**Whether the model's own warning counts is a profile setting.** Each Command Safety
profile has **What counts as a dangerous command**:

| Choice | What decides "dangerous" |
|---|---|
| **The AI's warning and my list** (shipped) | Either one is enough. A command the model flagged stays dangerous whatever the list says, and a matched pattern is recorded alongside the model's flag. The list is an independent safety net on top of the model, so a model mistake, or prompt injection arriving through terminal output, is not the only thing standing between a destructive command and an unattended run |
| **Only my list** | The list is the only classifier. If the model warns that a command is destructive, the card shows "The AI flagged this command as destructive. This connection's Command Safety profile only treats your own patterns as dangerous, so that warning was not applied." With an empty list, nothing counts as dangerous |

A dangerous command always needs a click, under every setting, and a typed reason while
the profile's **Make me type a reason for dangerous commands** is on.

The approval card says which fired — the model's own assessment or a pattern match —
and where a pattern fired it also names the pattern and the profile it came from.

## Why substrings and not regexes

The audience is infrastructure and DevOps engineers, who are not necessarily
regex-fluent, and plain substrings are quicker to write and review than regular
expressions. A badly written regular expression is also a hang risk that a substring
list does not have.

The product makes the opposite choice for custom *redaction* patterns, which are
real regular expressions and are consequently checked with a hard time limit before
they can be saved. Dangerous patterns need no validation because there is nothing in
them to go wrong. See [Redaction categories](redaction-categories.md).

## Extending the list

Patterns live in a Command Safety profile, not in a global setting. Edit the
**Default** profile's list, or clone it into a named profile, at
**Settings → Security → Command Safety → Manage profiles…**. The editor holds the
profile's three rules — **Run commands without asking me**, **What counts as a
dangerous command** and **Make me type a reason for dangerous commands** — above the
pattern list, an **Add** field, and **Reset to defaults**, which restores the default
patterns and the cautious rules (**Ask me every time**, **The AI's warning and my
list**, typed reason on). Nothing is kept until you choose **Save**.

![The Default Command Safety profile editor: the three rules, the What this means right now panel, the pattern list starting with rm -rf, Add, and Reset to defaults](../assets/images/29-command-safety-profile-editor.png)
_The editor for the Default profile. The rules and the summary sit above the list, so
the effect of a change is visible before **Save**; the list holds the 21 default
patterns._

Resolution is connection → group → Default:

```text
the session's connection's assigned profile
  → else its group's assigned profile
  → else the Default profile
```

A profile **replaces** the list, it does not extend it. A "Lab" profile can
legitimately hold fewer patterns than Default. Per-connection and per-group
assignment is the mechanism — there is no inheritance of one list into another.

### Additions per estate

The defaults cover the generic Unix, database, systemd and Kubernetes cases. What
they cannot cover is your platform. Check each candidate against the default list
first, because most of the obvious ones are already there:

| Candidate | Status | Note |
|---|---|---|
| `terraform destroy` | Not in defaults | Worth adding wherever Terraform is used |
| `openstack server delete` | Not in defaults | OpenStack estates |
| `openstack volume delete` | Not in defaults | OpenStack estates |
| `ceph osd rm` | Not in defaults | Ceph clusters |
| `oc delete project` | Not in defaults | OpenShift — the analogue of `kubectl delete namespace` |
| `> /dev/sd` | Not in defaults | Catches a redirect straight onto a block device |
| `kubectl delete` | **Broader than the defaults** | Defaults already cover `kubectl delete namespace` and `kubectl delete pv`. The bare prefix promotes every `kubectl delete`, including a single pod |
| `rm -rf` | **Already a default** | |
| `drop table` | **Already a default** | |
| `mkfs` | **Already a default** | |
| `dd if=` | **Already a default** | |
| `shutdown` | **Already a default** | |

!!! note "Check the defaults before you add"
    Most of the commands people reach for first — `rm -rf`, `drop table`, `mkfs`,
    `dd if=`, `shutdown` — already ship in the Default profile. Check the list above
    before adding, and spend the effort on the estate-specific verbs instead, which
    is where no default can help you.

Two things to keep in mind when adding a pattern. Substring matching means a short
pattern is a wide net — `truncate` already promotes `truncate -s 0 file.log` as well
as SQL. And because a profile replaces rather than extends, a new profile cloned from
a sparse one does not inherit the 21 defaults; clone from Default if you want them.

## See also

- [Command Safety profiles](../safety/command-safety-profiles.md) — creating and
  assigning profiles
- [Execution boundary & risk tiers](../safety/risk-tiers.md) — what a match changes
- [Approvals](../safety/approvals.md) — the approve & run / dismiss decision
- [Redaction categories](redaction-categories.md) — where the product does use real regexes
