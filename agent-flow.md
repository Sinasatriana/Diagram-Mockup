
# Agent Flow Diagram – Support Assistant

This markdown file contains a Mermaid diagram that illustrates the logic behind a Copilot agent designed to dynamically query Jira and Confluence to suggest solutions for support tickets.

## How to view this diagram on GitHub
- Save this file with the extension `.md` (e.g., `agent-flow.md`).
- Push it to a GitHub repository.
- Open the file in GitHub and switch to the **Preview** tab.
- GitHub will automatically render the Mermaid diagram.

## Diagram
```mermaid
flowchart LR
  subgraph U[User]
    A[User input:
• Free text question
• Ticket ID (e.g., #12345)]
  end

  subgraph AG[Copilot Agent (Logic)]
    E[Guardrails & Pre-processing
• PII redaction
• Language detection
• Normalization]
    B[Intent & entity detection
• Classify intent (search/triage)
• Extract entities (ticket ID, product, error)]
    C{Contains valid ticket ID?}
    D[Query builder
• Build Jira JQL / Confluence query
• Expand with synonyms & product taxonomy]
    G[Fetch from Jira
• getIssue/ search similar issues
• pull status, resolution, owner]
    H[Fetch from Confluence
• search pages / labels
• retrieve excerpts]
    I[Retrieval & ranking
• Hybrid search (BM25 + embeddings)
• Deduplicate & score by relevance]
    J[Answer synthesis
• Summarize findings
• Cite Jira issues & Confluence pages
• Generate next best actions]
    K{Confidence ≥ threshold?}
    L[Clarifying question
• Ask for missing details
• Offer disambiguation options]
    M[Action suggestions
• Open Jira issue
• Open Confluence article
• Add comment to ticket
• Create sub-task / link KB]
    N[Feedback & learning
• Thumbs up/down
• Capture chosen action
• Improve ranking signals]
    O{Escalation needed?}
    P[Human hand-off
• Assign to support queue
• Create escalation note
• Attach conversation context]
    Q[End]
  end

  subgraph INT[Integrations & Data]
    UAUTH[(Auth: SSO/OAuth)]
    R[(Jira API)]
    S[(Confluence API)]
    T[(Vector / Index store)]
  end

  subgraph GOV[Governance & Ops]
    X[Policy & rate limiting]
    Y[Error handling & retries]
    V[Telemetry & metrics]
    W[Audit log]
  end

  A --> E --> B --> C
  C -- "Yes" --> D
  C -- "No" --> D
  D --> UAUTH
  UAUTH --> R
  UAUTH --> S
  R --> G
  S --> H
  G --> I
  H --> I
  T --> I
  I --> J --> K
  K -- "Yes" --> M --> N --> O
  K -- "No" --> L --> D
  O -- "No" --> Q
  O -- "Yes" --> P --> Q
  E -.-> X
  D -.-> X
  R -.-> Y
  S -.-> Y
  J -.-> V
  M -.-> V
  N -.-> V
  A -.-> W
  J -.-> W
  P -.-> W
```
