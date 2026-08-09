# Knowledge Map

Use Mermaid graphs only. Keep the global map compact and connected.

```mermaid
graph TD
    Learning[Learning] --> Schema[Reusable schemas]
    Schema --> MentalModel[Mental models]
    MentalModel --> Transfer[Transfer tasks]
    Transfer --> Review[Review prompts]
    Schema --> GoCore[Go core: slice map string]
    GoCore --> GoAlgo[Go data structures and algorithms]
    GoAlgo --> Transfer
```

## Rules

- Add a node only when it helps future learning.
- Edges should mean depends on, builds on, is a type of, is part of, is used in, or transfers to.
- Avoid disconnected or decorative nodes.


<!-- study-agent-visual-contract:v1 -->
## Visual Contract

- This file is the global cross-topic and cross-chapter knowledge map.
- Use Mermaid for this global map.
- Keep chapter maps and concept-local diagrams canonical inside their notes; do not duplicate them here.
<!-- /study-agent-visual-contract:v1 -->
