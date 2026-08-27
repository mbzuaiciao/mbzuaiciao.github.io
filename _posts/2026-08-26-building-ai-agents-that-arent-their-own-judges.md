---
layout: post
title: "Building AI Agents That Aren't Their Own Judges: Lessons from Institutional Risk Agent Fleet"
date: 2026-08-26
categories:
  - ai-agents
  - agent-governance
tags:
  - Gemini
  - Google-ADK
  - agentic-ai
  - financial-ai
  - Google-Cloud
---

> **This article was created for the purpose of entering the All Things Agentic Hackathon.**

**#AllThingsAgentic**

When autonomous AI agents are introduced to high-stakes enterprise domains—such as institutional credit analysis, capital allocation, or treasury risk—the conventional demonstration pattern is familiar: an LLM is given a broad system prompt, a set of unconstrained API tools, and the autonomy to inspect data, draw conclusions, and trigger downstream actions. When an error occurs, the typical engineering reflex is to add "self-reflection" loops or spawn an evaluator agent to check the first agent's work.

In institutional environments, this design pattern is fundamentally fragile. Asking a probabilistic model to evaluate another probabilistic model creates an infinite regress of statistical consensus without deterministic mathematical guarantees. When money, regulatory compliance, and fiduciary obligations are at stake, autonomous agents cannot be their own judges.

In building the **Institutional Risk Agent Fleet** for the *All Things Agentic Hackathon*, our core premise was straightforward: **separate probabilistic reasoning from institutional authority**. Large language models excel at synthesizing unstructured context, formulating hypotheses, and adversarial exploration. But authority—enforcing tool permissions, verifying numerical calculations, maintaining immutable audit trails, and executing consequential actions—must remain deterministic, binding, and under human control.

---

## 1. The Core Architectural Philosophy: Reasoning vs. Authority

In institutional risk management, errors are not merely inconveniences; they have legal, capital, and reputational ramifications. A generative model may produce an articulate, highly plausible memo arguing that a counterparty's debt load is manageable. However, if that calculation dropped a subordinated debt tranche or misunderstood a reporting period, the fluency of the explanation is irrelevant.

To build a defensible autonomous system, we decoupled the architecture into three strictly isolated boundaries:

1. **Google Cloud Infrastructure Layer**: Provides managed, asynchronous transport and durable persistence (Cloud Run, Cloud Pub/Sub, Cloud Firestore, Cloud Logging).
2. **AI Reasoning Layer (Google ADK + Gemini 3.7 Flash)**: Provides parallel and sequential multi-agent analysis, structured claim synthesis, and adversarial challenges.
3. **Deterministic Governance & Authority Layer**: Enforces role-bound permissions, executes exact financial arithmetic, runs unit- and period-aware claim verification, maintains an append-only audit trail, and halts execution before any consequential state transition until an authorized human signs off.

```text
┌────────────────────────────────────────────────────────────────────────┐
│                        AI REASONING (PROBABILISTIC)                    │
│   Credit Agent ──┐                                                     │
│                  ├──> Investigator Agent ──> Challenger Agent          │
│   Market Agent ──┘                                 │                   │
└────────────────────────────────────────────────────┼───────────────────┘
                                                     ▼ Proposals Only
┌────────────────────────────────────────────────────────────────────────┐
│                  BINDING GOVERNANCE & VERIFICATION (DETERMINISTIC)     │
│   ReasoningStateAdapter ──> StructuredVerifier ──> GovernanceEngine   │
│   (Validates References)    (Unit/Value Match)     (Escalates Risk)   │
└────────────────────────────────────────────────────┬───────────────────┘
                                                     ▼
┌────────────────────────────────────────────────────────────────────────┐
│                       EXPLICIT HUMAN DECISION GATE                     │
│               Risk Officer Approval / Rejection / Escalation           │
└────────────────────────────────────────────────────────────────────────┘
```

The reasoning layer proposes structured artifacts; it possesses zero native authority to modify canonical state, bypass verification, or approve its own findings.

---

## 2. Multi-Agent Reasoning with Google ADK and Gemini 3.7 Flash

The reasoning subsystem uses **Google Agent Development Kit (ADK)** to orchestrate specialized **Gemini 3.7 Flash** agent roles. Rather than relying on a single monolithic prompt, the investigation is distributed across four distinct personas:

* **Credit Agent**: Ingests authoritative financial statements, debt schedules, and synthetic credit ratings. It focuses strictly on solvency metrics, leverage covenants, and liquidity profiles.
* **Market Agent**: Operates in parallel with the Credit Agent, analyzing synthetic credit default swap (CDS) spread movements, secondary bond pricing, and mark-to-market portfolio exposures.
* **Investigator Agent**: Gathers the parallel findings from Credit and Market, evaluates cross-domain correlations (e.g., how a 90 bps spread widening impacts portfolio value alongside debt maturities), and synthesizes a structured thesis containing concrete, testable claims.
* **Challenger Agent**: Acts as an internal adversarial critic. It inspects the Investigator's claims, searches for counter-evidence, flags potential unverified assumptions, and constructs alternative stress scenarios.

Crucially, **agents are not granted direct or arbitrary tool access**. Models do not invoke arbitrary shell commands or open database connections. Instead, all data retrieval and tool executions occur through deterministic gateways prior to reasoning or via structured proposal schemas.

Furthermore, the Challenger Agent's output is strictly advisory. A common architectural failure mode is giving critic agents the power to unilaterally overwrite or cancel previous findings. In our architecture, the Challenger can flag inconsistencies and propose revision targets, but only the deterministic **StructuredVerifier** has the authority to mutate a claim's validation status.

---

## 3. High-Level System Architecture

The end-to-end fleet deployment bridges asynchronous event ingestion on Google Cloud with the multi-agent reasoning loop and deterministic verification engine.

![Institutional Risk Agent Fleet Architecture]({{ '/assets/images/institutional-risk-agent-fleet-architecture.png' | relative_url }})
*Figure 1: Institutional Risk Agent Fleet system architecture. The design strictly separates managed Google Cloud infrastructure (blue), Gemini 3.7 Flash / Google ADK multi-agent reasoning (purple), and deterministic input controls, verification, and human authority (amber).*

### Asynchronous Pipeline: Pub/Sub to Cloud Run to Firestore

1. **Synthetic Event Ingestion**: An upstream risk event—such as ACME Corp credit spreads blowing out from 120 bps to 210 bps against a $75 million portfolio exposure—is published to a **Google Cloud Pub/Sub** topic.
2. **Private Worker Execution**: A private **Cloud Run** worker receives the authenticated push delivery. Using an atomic idempotency lease in **Cloud Firestore**, the worker ensures that duplicate Pub/Sub delivery attempts do not trigger concurrent duplicate investigations.
3. **Deterministic Pre-Execution**: Evidence files are registered under immutable IDs (`evidence-filing-001`, `evidence-market-001`). Role-bound capability gateways execute required financial arithmetic in pure Python, attaching input parameters, output values, units, and timestamps.
4. **Structured Reasoning**: Google ADK coordinates the Gemini agent fleet, passing typed evidence snapshots and receiving strongly typed Pydantic proposal bundles.
5. **Deterministic Verification & Governance**: The worker passes proposals through reference validation, numerical verification, and governance rule evaluation.
6. **Persistence & UI Gate**: Canonical investigation snapshots, claim histories, and structured audit events are committed to Firestore. A public **Cloud Run Streamlit UI** polls the worker API to display the real-time investigation state to risk officers.

---

## 4. The 4.1x → 3.8x Verification Walkthrough: Structured vs. Lexical Verification

Many generative AI systems attempt "verification" through lexical comparison or prompt-based evaluation: asking an LLM, *"Does Claim A agree with Document B?"*

This approach fails in quantitative domains for three reasons:
1. **High Semantic Overlap**: The sentence *"ACME leverage is 4.1x"* and *"ACME leverage is 3.8x"* have >85% lexical similarity and identical semantic structure. Embedding-based cosine similarity or naive LLM judges will often mark them as consistent.
2. **Unit & Period Blindness**: An LLM judge may easily conflate `3.8%` with `3.8x`, or compare an FY2024 metric against an FY2025 metric without raising an error.
3. **Non-Deterministic Fluctuation**: Asking a probabilistic model to verify mathematical equality introduces temperature variance and non-reproducible outcomes.

### The Lifecycle of `claim-001` and `claim-002`

In our scenario, an upstream synthetic news summary incorrectly claims that ACME Corp's FY2025 debt-to-EBITDA leverage has risen to **4.1x**.

```text
Round 1:
  Investigator proposes: claim-001 (value = 4.1x, metric = "leverage", period = "FY2025")
  StructuredVerifier checks:
    - Authoritative Filing (evidence-filing-001): 3.8x
    - Deterministic Tool (tool-execution-001): 3.8x
    - Comparison: |4.1 - 3.8| = 0.3 > 1e-9  --> FAILED
  Result: claim-001 status set to REJECTED.

Round 2 (Revision):
  Workflow triggers revision loop citing authoritative calculation.
  Investigator proposes: claim-002 (value = 3.8x, supersedes = "claim-001")
  StructuredVerifier checks:
    - Authoritative Filing: 3.8x
    - Deterministic Tool: 3.8x
    - Comparison: |3.8 - 3.8| <= 1e-9        --> PASSED
  Result: claim-002 status set to VERIFIED.
```

The mathematical evaluation is implemented in deterministic Python:

```python
# Evaluated deterministically inside StructuredVerifier:
expected = expected_authoritative_values[0]  # 3.8 from audited filing
observed = float(claim.value)                # 4.1 from proposed claim

# Unit agreement and strict numeric tolerance:
if unit_conflicts:
    return Finding(result=FindingResult.FAILED, rationale=f"Unit conflict: {unit_conflicts}")

passed = not support_disagrees and abs(observed - expected) <= 1e-9
```

Because `abs(4.1 - 3.8) = 0.3 > 1e-9`, the finding fails immediately. No amount of plausible prose from the model can bypass this check.

---

## 5. Why Rejected Claims Must Remain in History

A common anti-pattern in agentic engineering is "silent overwriting": when an agent makes an error, the system mutates the existing record in-place or deletes the faulty claim.

In regulated institutions, silent overwriting destroys the **audit trail**. An institutional regulator or chief risk officer does not just want to see the final sanitized output; they need to know:
* What did the autonomous system initially infer from noisy upstream data?
* Which specific tool or evidence source debunked the initial hypothesis?
* Who or what initiated the correction, and when?

For this reason, our domain model implements **explicit supersession**:

```python
class Claim(BaseModel):
    id: str                                  # e.g., "claim-002"
    metric: str                              # "leverage"
    value: float                             # 3.8
    unit: str                                # "x"
    period: str                              # "FY2025"
    status: ClaimStatus                      # VERIFIED
    supersedes_claim_id: str | None = None   # "claim-001"
```

When `claim-001` fails verification, its status is permanently recorded as `REJECTED`. When `claim-002` is generated, it contains an explicit reference `supersedes_claim_id="claim-001"`. The audit log records both rounds sequentially. The final report displays the complete lineage: the error was caught, rejected by rule, and corrected by authoritative evidence.

---

## 6. Strict Model-Output Validation vs. "Silent Self-Healing"

Many agent frameworks promote "loose input handling"—if an LLM returns JSON with missing fields, hallucinated IDs, or extra keys, the framework quietly attempts to coerce or patch the data.

In an institutional risk engine, **silent coercion is a critical vulnerability**. If a model invents an evidence ID (e.g. citing `evidence-sec-999` which was never ingested) or attributes a calculation to an unexecuted tool, silently repairing or ignoring that field allows ungrounded hallucinations to contaminate canonical state.

Our `ReasoningStateAdapter` enforces a strict trust boundary:

1. **Schema Validation**: Raw Gemini output must conform to strict Pydantic schemas.
2. **Reference Validation**: Every cited evidence ID, tool execution ID, and premise ID is cross-referenced against the `Investigation` register. If any ID does not exist, the adapter raises `ReasoningOutputError` and rejects the outcome.
3. **Collision Detection**: If an agent attempts to reuse an existing proposal ID with conflicting values, the collision is rejected rather than silently resolved via "first-wins" or "last-wins".
4. **Role Attribution Invariants**: A Credit Agent cannot author claims assigned to Market or Investigator.

By failing fast on unsupported model behavior, the system prevents subtle hallucination drift from ever entering persisted storage.

---

## 7. Machine Verification $\neq$ Institutional Authorization

Perhaps the most critical governance lesson from building the fleet is the separation between **factual correctness** and **institutional risk acceptance**:

$$\text{Machine Verification Passed} \not\equiv \text{Action Authorized}$$

Consider what happens when Round 2 completes in our ACME scenario:
* Leverage is verified at 3.8x.
* Spread delta is verified at +90 bps.
* Mark-to-market loss is verified at -$2.835 million.

Every claim in the investigation has achieved `FindingResult.PASSED`. Does this mean the system should automatically clear the event and archive the alert?

**Absolutely not.**

Our domain model decouples the evaluation across **four orthogonal dimensions**:

```text
┌─────────────────────────────────────────────────────────────┐
│ 1. LifecycleStatus:                                         │
│    CREATED → INVESTIGATING → VERIFYING →                    │
│    REVISING → AWAITING_HUMAN_REVIEW → COMPLETED             │
├─────────────────────────────────────────────────────────────┤
│ 2. GovernanceStatus:                                        │
│    CLEAR | REVISION_REQUIRED | HUMAN_REVIEW_REQUIRED       │
├─────────────────────────────────────────────────────────────┤
│ 3. RiskClassification:                                      │
│    GREEN | AMBER | RED                                      │
├─────────────────────────────────────────────────────────────┤
│ 4. HumanReviewStatus:                                       │
│    NOT_REQUIRED | REQUIRED | APPROVED | REJECTED | ESCALATED│
└─────────────────────────────────────────────────────────────┘
```

When the `GovernanceEngine` evaluates the verified investigation:
1. `machine_verification_passed` is `True`.
2. The engine checks institutional risk policy rules:
   * Event severity is `HIGH`.
   * Portfolio exposure is $\$75,000,000$, which exceeds the $\$50,000,000$ institutional threshold.
3. Because risk rules are triggered, `RiskClassification` is set to **`RED`** and `GovernanceStatus` becomes **`HUMAN_REVIEW_REQUIRED`**.
4. The workflow transitions `LifecycleStatus` to **`AWAITING_HUMAN_REVIEW`** and halts.

Passing machine verification simply guarantees that the risk metrics are mathematically true. It does not decide whether the institution should maintain the credit line, hedge the exposure, or exit the position. That decision belongs exclusively to the authorized human risk officer.

---

## 8. Summary of Lessons Learned for Enterprise Agent Governance

Building the Institutional Risk Agent Fleet reinforced several foundational principles for deploying agentic AI in mission-critical environments:

1. **More Agents Do Not Equal More Reliability**: Multiplying unconstrained agents increases entropy, not safety. Reliability comes from placing deterministic guardrails around agent boundaries.
2. **Keep the Model in the Proposal Space**: Use LLMs for what they do best—hypothesizing, summarizing complex text, and challenging assumptions. Keep permissions, calculation execution, and state transitions in deterministic code.
3. **Preserve Errors for Auditability**: An immutable ledger of rejected claims and superseding corrections creates defensibility and transparency.
4. **Deterministic Gateways Over Tool Autonomy**: Restrict evidence access and financial tools through role-scoped code gateways rather than providing arbitrary tool execution environments.
5. **The Human Gate Must Be Architectural, Not Cosmetic**: The inability of the software to transition to `COMPLETED` without an authenticated human signature ensures that the machine remains an advisor, never an unaccountable judge.

---

## Source Code & Live Demonstration

The complete, open-source implementation—including the deterministic verification engine, ADK agent workflows, Google Cloud deployment infrastructure, and synthetic scenarios—is publicly available:

### Source code

[https://github.com/mbzuaiciao/institutional-risk-agent-fleet](https://github.com/mbzuaiciao/institutional-risk-agent-fleet)

### Live demo

[https://risk-fleet-ui-66597047772.us-central1.run.app](https://risk-fleet-ui-66597047772.us-central1.run.app)
