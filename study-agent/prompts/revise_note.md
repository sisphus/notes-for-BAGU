# Revise Note Prompt

Use this prompt when the user says a note is missing content, poorly structured, stale, too shallow, or not following `AGENTS.md`.

## Required First Pass

1. Read the relevant source material.
2. Read the existing note under `outputs/notes/`.
3. Read related diagram, global graph, schema ledger, weakness, and review files if they could be affected.
4. Identify whether the problem is:
   - missing source coverage
   - misplaced content
   - weak `Core Concepts`
   - narrative summary instead of schema-led notes
   - missing, misplaced, duplicated, or unreadable Visual Model
   - stale global graph or schema state
   - unclear examples or weak self-test questions

## Revision Rules

- Rewrite the affected section in the correct place.
- Prefer structural revision over append-only patches.
- Preserve useful existing material that is source-faithful.
- Remove duplicated, vague, or decorative content.
- Keep `Core Concepts` schema-led: definition, intuition, example, common mistake.
- Insert or revise a Visual Model immediately after the example or paragraph it explains; do not append a visual patch at the end.
- Use Mermaid by default and Excalidraw only for spatial or freely annotated structures that Mermaid cannot express clearly.
- Require a same-basename SVG or PNG preview for every Excalidraw source referenced by a note; otherwise replace the note visual with Mermaid.
- Keep the Chapter Knowledge Map focused and connected, and keep cross-topic relationships in `outputs/graph/knowledge_map.md`.

## Output Shape

After revising files, report:

```markdown
### Revised
- Note:
- Related artifacts:

### What Changed
- ...

### Verification
- Source reread:
- Structure checked:
- Visual placement and links checked:
- Remaining risk:
```

## Artifact Updates

- Update the note first.
- Update `outputs/diagrams/` only when an Excalidraw visual and its preview are added or materially revised.
- Update the global graph only when a cross-topic or cross-chapter relationship changed.
- Update schema ledger only when a reusable schema was added or materially clarified.
- Update weakness/review only if the revision was caused by a real learner misunderstanding.
