# Summarize Prompt

Use this prompt when the user asks for a summary, overview, notes, or a compact explanation of source material.

## Required First Pass

1. Read the source material first.
2. If the source is large, map headings and section order before summarizing.
3. Read existing notes if present to avoid duplicating or contradicting prior artifacts.
4. Identify whether the user wants chat-only summary or persistent notes.
5. For persistent notes, run the selective visual-needs scan from `AGENTS.md`; diagrams remain optional and must answer a specific learning question.

## Summary Principle

A useful learning summary separates:

- Information: facts, claims, definitions, examples.
- Representations: key concepts, variables, objects, and boundaries.
- Schemas: reusable structures and trigger situations.
- Mental models: causal chains or runnable mechanisms.
- Transfer use: where the learner should apply the knowledge.

Do not treat a short summary as evidence that learning has happened. A summary should create the next learning entry point.

## Default Output

In chat, provide:

```markdown
### Source-Grounded Overview
...

### Core Roadmap
1.
2.
3.

### Key Schemas
- Schema:
  Trigger:
  Use:

### Recommended Next Step
...
```

If full notes are useful, write them to `outputs/notes/<topic>_notes.md` using the notes contract in `AGENTS.md`, then mention the path in chat.

## Persistent Notes Rules

Full notes should include:

1. Topic Overview.
2. Core Concepts.
3. Deep Understanding.
4. Minimal Working Example.
5. Chapter Knowledge Map.
6. Self-Test Questions.
7. Weak Point Detection.

Core Concepts must be schema-led. Avoid flat lists of facts or copied section headings without learning structure. Place any local Visual Model beside the example or mechanism it explains rather than collecting diagrams in section 5.

## Artifact Updates

- Update notes when the summary is durable or source coverage matters.
- Store Excalidraw source and preview pairs under `outputs/diagrams/<topic>/` only when Mermaid cannot express the required spatial model clearly.
- Update `outputs/graph/knowledge_map.md` only for durable cross-topic or cross-chapter relationships.
- Update schema ledger only for reusable schemas that the user is likely to practice.
- Do not update error or weakness files unless the user answer reveals a mistake.
