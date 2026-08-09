# Knowledge Map

Use Mermaid graphs only. Keep the global map compact and connected.

```mermaid
graph TD
    Learning[Learning] --> Schema[Reusable schemas]
    Schema --> MentalModel[Mental models]
    MentalModel --> Transfer[Transfer tasks]
    Transfer --> Review[Review prompts]
    NetworkTLS["TLS handshake schemas"] -->|"is a type of"| Schema
    RSATLS["TLS 1.2 static RSA"] -->|"transfers to"| ECDHETLS["TLS 1.2 ECDHE-RSA"]
    DH["Diffie-Hellman agreement"] -->|"is used in"| ECDHETLS
    ECDHETLS -->|"is part of"| NetworkTLS
    ECDHETLS -->|"is used in"| ForwardSecrecy["Forward secrecy model"]
    TCPFeature["TCP feedback and window schemas"] -->|"is a type of"| Schema
    TCPFeature -->|"builds on"| TCPConnection["TCP connection and byte-stream state"]
    TCPFeature -->|"is used in"| HTTP2HOL["HTTP/2 TCP head-of-line blocking"]
    HTTP11Limit["HTTP/1.1 performance limits"] -->|"transfers to"| HTTP2Design["HTTP/2 framing and multiplexing"]
    HTTP2Design -->|"builds on"| TCPConnection
    HTTP2HOL -->|"is part of"| HTTP2Design
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
