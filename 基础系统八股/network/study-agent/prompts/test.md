# Test Prompt

Use this prompt when the user asks to be quizzed, tested, reviewed, or challenged.

## Required First Pass

Before asking a question:

1. Read `outputs/schemas/schema_ledger.md` to identify active or forming schemas.
2. Read `outputs/weaknesses/profile.md`, `outputs/errors/error_log.md`, and `outputs/review/schedule.md` if present.
3. Prefer active weaknesses, missed concepts, and due review prompts over random topics.
4. If no state exists, ask one source-grounded question from the relevant material.

## Question Selection

Choose one question type:

- Recall: checks a definition or distinction.
- Completion: asks the learner to fill one missing step.
- Near transfer: same structure, changed surface details.
- Far transfer: different domain, same deep structure.
- Error diagnosis: asks what is wrong with a flawed answer or scenario.

Increase difficulty only after the learner succeeds. If the learner struggles, narrow the next question.

## Rules

- Ask exactly one question at a time.
- Do not reveal the answer before the learner attempts it.
- Do not make all multiple-choice answers use the same letter.
- Keep the question short enough to answer in one message.
- Prefer transfer and error diagnosis for schemas that are already forming.

## Evaluation Format

After the learner answers, respond with:

```markdown
### Evaluation
- Correct:
- Missing:
- Incorrect:

### Error Type
...

### Repair
...

### Targeted Exercise
...
```

If the answer is correct, explain why briefly and either strengthen the schema with a harder transfer task or mark the schema as more stable. If the answer is wrong, repair only the weak boundary before moving on.

## Artifact Updates

- Log real mistakes in `outputs/errors/error_log.md`.
- Update `outputs/weaknesses/profile.md` when a mistake repeats or reveals an important gap.
- Update `outputs/review/schedule.md` for weak or newly repaired concepts.
- Update `outputs/state/current_session.md` with the current question and next action.
