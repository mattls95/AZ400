# Deployment Process

## Purpose

This document describes the process used to move a change from development to production.

## Process

```mermaid
flowchart LR
    A[Developer] --> B[Pull Request]
    B --> C[CI]
    C --> D{Checks pass?}
    D -->|Yes| E[Merge to main]
    D -->|No| B
    E --> F[Deploy]
```

A few pieces to understand:

flowchart LR means we're creating a flowchart laid out left-to-right.

A[Developer]

defines a node.

A --> B

defines a directional connection.

And:

D{Checks pass?}

uses braces to create a decision-style node.

Finally:

D -->|Yes| E

puts Yes on the connecting edge.

```mermaid
sequenceDiagram
    Developer->>GitHub: Push change
    Developer->>GitHub: Open PR
    GitHub->>CI: Trigger pipeline
    CI-->>GitHub: Test results
    GitHub-->>Developer: Status check
```

## Rules

- Changes must be submitted through a pull request.
- Required CI checks must pass.
- Changes are merged into `main` before deployment.