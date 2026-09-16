# Redaction categories

The ten built-in redaction categories, their ids and what each one matches. The id is
what a Data Handling profile stores and what you select in the profile editor; the
label is what the editor shows beside it.

| Id | Label | Default | Matches |
|---|---|---|---|
| `aws_access_key` | AWS access keys | On | `AKIA` followed by 16 upper-case alphanumerics |
| `private_key_block` | Private key blocks | On | A whole PEM block, `-----BEGIN … PRIVATE KEY-----` through its matching `-----END-----` |
| `bearer_token` | Bearer tokens | On | The literal `Bearer`, whitespace, then the token |
| `jwt` | JWTs | On | `eyJ` then three base64url segments joined by dots |
| `password_assignment` | Password/secret assignments | On | `password`, `passwd`, `pwd` or `secret`, then `=` or `:`, then the value. Case-insensitive |
| `ipv4` | IPv4 addresses | Off | A dotted quad with each octet range-checked to 0–255 |
| `ipv6` | IPv6 addresses | Off | Full eight-group form and the common `::`-compressed forms |
| `uuid` | UUIDs | Off | The standard 8-4-4-4-12 hex UUID/GUID form |
| `email` | Email addresses | Off | The standard `user@domain.tld` form |
| `hostname` | Hostnames / FQDNs | Off | Dotted hostnames, e.g. `bastion-1.devops.internal` |

Five on, five off. The five that are on are the ones whose false-positive cost is
effectively zero: an `AKIA…` string, a PEM block, a `Bearer` header, a JWT or a
`password=` assignment is a secret and nothing else.

## Structural categories

The five off-by-default categories match structure, not secrecy. Redacting every IP
address, hostname and UUID from terminal output makes infrastructure diagnosis
considerably harder — the model can no longer tell you which node is unhealthy,
which volume failed to attach or which endpoint is refusing connections, because
every identifier in the output has become the same placeholder. Turn them on where
the estate's topology is itself sensitive, and expect diagnosis to be less precise
in exchange.

## The replacement placeholder

A match is replaced with `[REDACTED:<id>]` — the category's own id, not a generic
mask. A redacted AWS key becomes `[REDACTED:aws_access_key]`, and a custom pattern
becomes `[REDACTED:<its name>]`. The model is told that something was removed and
what kind of thing it was, which is what lets it reason about the line at all.
Matches are also counted per category, which is what the redaction badge reports.

## Custom pattern validation

The built-in categories are plain on/off toggles and need no validation: they ship
with the product, and each is written to run in linear time.

A custom pattern is a regular expression you write, and it runs synchronously on the
path every AI request goes through, so a pathological pattern could stall the
application rather than merely misbehave. Custom patterns are therefore checked when
you save them.

| | Built-in categories | Custom patterns |
|---|---|---|
| What it is | A vetted pattern behind a toggle | A regular expression you write |
| Checked when saved | Not needed | Yes |
| Time allowed | — | 250 ms |
| If it takes longer | — | Never saved |

Two checks run in order. A cheap compile catches a plain syntax error instantly with
a clear message. The pattern is then run against inputs designed to provoke
catastrophic backtracking — long runs capped with a non-matching trailing character
— and if it has not finished within the time allowed it is refused rather than
warned about.

At match time each pattern is applied independently, so a pattern that compiled but
throws on some input is skipped rather than aborting the pass and leaving every
later pattern's secrets in place.

## Where categories are set

**Settings → Security → Data Handling → Manage profiles…**. Categories are per Data
Handling profile, and profiles resolve connection → group → Default. A profile
replaces rather than extends, so a profile can legitimately be less strict than
Default. A category missing from a saved profile — because the profile predates the
category — falls back to that category's own default state rather than being
dropped.

Redaction itself is not a toggle. It happens on one code path, before anything
leaves the machine, for a configured provider and a connected assistant alike.
**Show redaction badge** changes only whether you are told; hiding the badge does not
disable redaction.

## See also

- [Data Handling profiles](../safety/data-handling-profiles.md) — the editor, and
  custom patterns
- [What gets sent](../ai/what-gets-sent.md) — the full egress path
- [Security model](../safety/security-model.md) — the rationale in full
- [Settings map](settings-map.md) — where the controls live
