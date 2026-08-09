# Diagrams

Store editable Excalidraw visual models and their generic Markdown previews here. Inline Mermaid belongs in the note that uses it and must not be copied into this directory.

## Layout

Use one topic directory and a stable, descriptive diagram slug:

```text
outputs/diagrams/<topic>/<diagram-slug>.excalidraw
outputs/diagrams/<topic>/<diagram-slug>.svg
```

Use `.png` only when an SVG preview is unavailable. Prefer lowercase kebab-case for English slugs; for non-English topics, use a stable descriptive name without spaces.

## Excalidraw Contract

- Use Excalidraw only when spatial layout, layered boundaries, memory or page layout, tree structure, physical placement, or free-form annotation is essential.
- Keep the `.excalidraw` source and its `.svg` or `.png` preview on the same basename.
- A note must embed the preview and link the editable source using paths relative to the note's actual directory.
- A bare `.excalidraw` link is supplementary, not a complete visual model for generic Markdown readers.
- If a preview cannot be produced, use Mermaid in the note instead.
- Keep diagrams below 20 elements when possible and remove decorative or unsupported relationships.

## Note Reference Shape

Compute the exact relative prefix from the note's location:

```markdown
![Visual Model: question answered by the diagram](<relative-path>/<diagram-slug>.svg)
[Editable Excalidraw source](<relative-path>/<diagram-slug>.excalidraw)
```

Follow the preview with a short reading path and a source anchor. Add a boundary statement when the visual intentionally simplifies the source.

## Validation

- Confirm the `.excalidraw` file is valid JSON.
- Confirm the same-basename SVG or PNG preview exists and opens.
- Confirm the note's relative preview and source links resolve from the note's directory.
