# Reference

Lookup tables rather than explanation. Each page below leads with its table and
records the values as they ship, so you can check a port, a default, a field or a
pattern without reading a chapter to find it.

If you are looking for the reasoning behind a value rather than the value
itself, the narrative sections are the better read: [Safety &
security](../safety/index.md) for the safety machinery, [AI](../ai/index.md) for
the provider and assistant modes, and [Connections](../connections/index.md) for
connecting to things.

## The pages

[Connection fields](connection-fields.md)
:   Every field the new-connection and edit-connection dialogs can show, per
    connection type, as a type-by-field matrix. Read it when you are filling in
    a form and want to know why a row appeared or vanished.

[Settings map](settings-map.md)
:   Which of the seven settings pages holds which control, and what each control
    does and defaults to. Read it when you know what you want to change but not
    where it is.

[AI provider matrix](provider-matrix.md)
:   The ten providers, how each authenticates, what credential fields it asks
    for, whether its endpoint is fixed, and whether you can type your own model
    name. Read it when choosing a provider or adding an unlisted one.

[MCP tools](mcp-tools.md)
:   The complete tool surface an external AI Assistant gets — six tools, their
    parameters, what they return, whether output is redacted and which one
    requires approval. Read it during a security review of the assistant
    integration.

[Redaction categories](redaction-categories.md)
:   The ten built-in categories, their ids, what each matches and which five are
    on by default. Read it when building a Data Handling profile.

[Dangerous patterns](dangerous-patterns.md)
:   The default dangerous-command list in full, how substring matching works,
    and what to add for your own estate. Read it before writing a Command Safety
    profile.

[Network requirements](network-requirements.md)
:   Every network path the product uses, with default ports and directions, for a
    firewall or security review. Read it when someone asks what OpsPilot needs
    to reach.

[Keyboard shortcuts](keyboard.md)
:   The bindings the application listens for, by panel. Read it to work faster.

[Glossary](glossary.md)
:   The product's terms, with the distinctions that matter kept sharp. Read it
    first if a term is being used somewhere in a way you do not recognise.

## See also

- [Safety & security](../safety/index.md) — why the safety values are what they
  are
- [AI Providers](../ai/providers.md) — configuring a provider, rather than the
  matrix of them
- [Troubleshooting](../operations/troubleshooting.md) — symptom-first, when
  something is not working
