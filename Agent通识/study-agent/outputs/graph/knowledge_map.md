# Knowledge Map

Use Mermaid graphs only. Keep the global map compact and connected.

```mermaid
graph TD
    Learning["Learning"] --> Schema["Reusable schemas"]
    Schema --> Agent["AI Agent mental model"]
    LLM["LLM decision"] --> Agent
    Context["Context observation"] --> Agent
    Tools["Tool action"] --> Agent
    ContextEngineering["Context engineering"] -->|"is used in"| Harness
    StaticPrefix["Stable system and tools"] -->|"is used in"| ContextEngineering
    Trajectory -->|"is used in"| ContextEngineering
    Skills["Progressive skills"] -->|"is used in"| ContextEngineering
    StatusBar["Explicit status bar"] -->|"is used in"| ContextEngineering
    Compression["Task-aware compression"] -->|"is used in"| ContextEngineering
    LLM --> ReAct["ReAct loop"]
    Context --> ReAct
    Tools --> ReAct
    ReAct --> Trajectory["Trajectory"]
    Harness["Harness"] --> Agent
    Context --> Harness
    Tools --> Harness
    Guardrails["Constrain, verify, correct"] --> Harness
    Orchestration["Workflow or autonomous"] --> Harness
    Agent --> Transfer["Transfer tasks"]
    Transfer --> Review["Review prompts"]
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
