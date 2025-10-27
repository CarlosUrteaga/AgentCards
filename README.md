# AgentCard

The rapid adoption of large language models (LLM) into AI agents has created new challenges for transparency, reproducibility, and governance. While prior artifacts such as Model Cards and Data Sheets for Datasets support documentation of models and data, no analogous standard exists for describing the operational characteristics of AI agents. This paper introduces Agent Cards, a structured documentation artifact designed to capture the essential attributes of an agent, including its roles, memory taxonomy, tool integrations, communication protocols, monitoring hooks, governance scope, and evaluation metrics. By standardizing how agents are described, Agent Cards provide a lightweight yet powerful tool for enabling transparency, comparability, and auditability across deployments. We present a template, illustrate its application through an example, and discuss the benefits of adopting Agent Cards as part of broader MLOps/LLMOps practices. We argue that Agent Cards can serve as a foundation for future work on agent ledgers, audit bundles, and maturity frameworks, offering practitioners and researchers a common vocabulary for responsible operationalization of agentic AI.

Table 1 Proposed Agent Card Template

| **Section**           | **Description**                                                                                                                                                                                                                                           |
| --------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Agent version**     | Semantic version of the agent release (e.g., 1.2).                                                                                                                                                                                                        |
| **Agent Name**        | Identifier of the agent.                                                                                                                                                                                                                                  |
| **Agent Role(s)**     | Planner, Executor, Critic, Orchestrator (list specific roles).                                                                                                                                                                                            |
| **Inputs**            | Text files, APIs, structured and unstructured data.                                                                                                                                                                                                       |
| **Outputs**           | API responses or text.                                                                                                                                                                                                                                    |
| **Memory**            | Short-term: current turn/context window profile; Long-term.                                                                                                                                                                                               |
| **Tools/Functions**   | Capabilities the agent can invoke beyond its core LLM, such as calculators, retrieval modules, external APIs, internal spreadsheets, or domain-specific tools. Document the type of tool, its intended purpose, and how it extends the agent’s abilities. |
| **Communication**     | Human interface (chat/UI); agent-to-agent protocols; message schemas/versions; handoff/approval policies.                                                                                                                                                 |
| **Monitoring**        | Logged metrics (latency, token usage, error rate); trace IDs; inference profile/feature flags; SLOs and alert routes.                                                                                                                                     |
| **Governance**        | Safety filters/guardrails; PII/PHI handling; data retention and access control; approvals and audit checkpoints.                                                                                                                                          |
| **Versioning**        | Release tag/date; prompt hash; toolchain/SBOM; external dependency versions; overall reproducibility hash.                                                                                                                                                |
| **Known Limitations** | Current scope boundaries; partial automation notes; known brittleness or non-determinism sources (e.g., upstream API variability).                                                                                                                        |
| **Evaluation**        | Benchmarks/KPIs (e.g., RAG quality, long-context stress); calibration/abstention policy; evaluation datasets/snapshots; last run date and results.                                                                                                        |

YAML for LLM
```
agent cards for ai agent
agentcard: 1.0
meta:
  name: TaxBot
  version: 0.7.3
  owner: Tax Operations — Data/AI
  last_updated: 2025-10-10
purpose:
  objective: "Assist with personal and business tax queries, document intake, and filing prep with traceable, policy‑compliant outputs"
  users: [Tax Analyst, Accountant, Taxpayer]
interface:
  inputs: [question, PDF, XML, XLSX]
  outputs: [answer, citation_list, filing_checklist, action_proposal]
tools:
  - name: tax_rules_db
    scope: read-only
    eligibility: requires jurisdiction and tax_year
  - name: parse_tax_pdf
    leakage_guard: no final filing decisions
  - name: sat_portal_client
    scope: read-only
    eligibility: authenticated user session
autonomy:
  allowed_actions: [draft_client_email, create_case_ticket]
  requires_approval_for: [submit_return, modify_client_profile]
memory:
  persistent: session_summaries (TTL: 30d)
  pii: masked_at_ingest; encryption_at_rest: AES256
policies:
  deference_gate: gamma=-3.0 until verify+readiness>=tau
  prohibited_content: [store raw IDs, off‑policy advice]
evaluation:
  kpis: {first_response_time_p50: "<30s", hallucination_rate: "<1%", citation_coverage: ">=95%"}
  red_team: [prompt_injection, refund_scam, identity_theft_vector]
ops:
  envs: [dev, staging, prod]
  logging: structured_traces to s3://agent-logs
  rollback: blue/green with canaries
risks:
  - name: pii_leakage
    mitigation: strict scopes + PII scrubbing + DLP scanners

```
