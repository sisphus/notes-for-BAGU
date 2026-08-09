# Knowledge Map

This file is the global cross-topic and cross-chapter knowledge map. Use Mermaid only and keep the network compact and connected. Chapter maps and concept-local diagrams remain canonical inside their notes; do not duplicate them here.

```mermaid
graph TD
    JavaSource["Java source"] -->|"is compiled into"| Bytecode["JVM bytecode"]
    Bytecode -->|"is executed by"| JVM["Platform JVM"]
    JVM -->|"is used in"| Portability["Cross-platform delivery"]
    JavaSource -->|"uses"| TypeSystem["Java type system"]
    TypeSystem -->|"is used in"| Overload["Overload resolution"]
    TypeSystem -->|"is used in"| Boxing["Boxing and wrappers"]
    JavaSource -->|"uses"| ObjectModel["Object model"]
    ObjectModel -->|"is used in"| Override["Override dispatch"]
    ObjectModel -->|"is used in"| InstanceState["Instance state"]
    ObjectModel -->|"is used in"| StaticState["Class-level static state"]
    TypeSystem -->|"is used in"| Generics["Generic type relations"]
    JavaSource -->|"uses"| AnnotationMetadata["Annotation metadata"]
    AnnotationMetadata -->|"is processed by"| Reflection["Runtime reflection"]
    ObjectModel -->|"is inspected by"| Reflection
    Reflection -->|"is used in"| DynamicProxy["Dynamic proxy"]
```

## Rules

- Add a node only when it helps future learning.
- Edges should mean depends on, builds on, is a type of, is part of, is used in, or transfers to.
- Avoid disconnected or decorative nodes.
- Add only relationships that remain useful across topic or chapter boundaries.
