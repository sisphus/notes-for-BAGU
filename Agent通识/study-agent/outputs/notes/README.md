# Notes

Store full source-grounded notes here.

Default filename patterns:

- `lecture_X_notes.md`
- `chapter_X_notes.md`
- `<topic>_notes.md`

Each note should follow the contract in `AGENTS.md`:

1. Topic Overview.
2. Core Concepts.
3. Deep Understanding.
4. Minimal Working Example.
5. Chapter Knowledge Map.
6. Self-Test Questions.
7. Weak Point Detection.

Core Concepts should be schema-led, not a loose summary of source headings.

## Visual Models

- Run a selective visual-needs scan while creating or revising a full note. Do not require a diagram for every schema.
- Place a concept-local Visual Model immediately after the concrete example or paragraph it explains.
- Put cross-schema mechanisms in `Deep Understanding` and execution traces in `Minimal Working Example`.
- Keep section 5 to one compact Chapter Knowledge Map instead of repeating every local diagram.
- Embed Mermaid directly in the note. Do not duplicate chapter Mermaid source in `outputs/graph/`.
- When Excalidraw is necessary, embed its SVG or PNG preview and link its same-basename editable source under `outputs/diagrams/<topic>/`.
- Compute asset links relative to the note's actual directory, including when notes use nested folders.
