---
layout: splash
title: "CV"
permalink: /cv/
author_profile: false
redirect_from:
  - /resume
---

{% include base_path %}

<div style="float: right;">
  <a href="{{ base_path }}/files/CV.pdf" class="btn btn--info"><i class="fa fa-file-pdf-o" aria-hidden="true"></i> Download PDF</a>
</div>

# CC
**Portfolio Manager & AI Researcher**  
[<i class="fa fa-envelope" aria-hidden="true"></i> ciao@gmail.com](mailto:ciao@gmail.com) | [<i class="fa fa-phone" aria-hidden="true"></i> +1 617-500-5555](tel:+16175005555) | <i class="fa fa-globe" aria-hidden="true"></i> USA / UK

---

<div style="font-family: Georgia, serif; font-size: 1.1em; line-height: 1.6;" markdown="1">

## Research Direction
My research investigates the **formalization of compositional reasoning** through representation geometry. I study how **algebraic and geometric inductive biases**—such as associativity, identity, and compositional refinement—can be treated as differentiable, **adaptive inductive priors** within neural representations. My goal is to move beyond scale-driven progress toward a theory of how **representation form** shapes reasoning capability, enabling robust **out-of-distribution compositional extrapolation**.

## Research Interests
*   **Adaptive Inductive Priors**: Differentiable constraints for structural regularity.
*   **Geometric Deep Learning**: Lie-theoretic perspectives on iterative neural refinement.
*   **Functorial Semantics**: Learned homomorphism and equivariance in latent spaces.
*   **Systematic Generalization**: Extrapolating to unseen multi-step and hierarchical compositions.

</div>

---

<div style="font-family: Georgia, serif; font-size: 1.1em; line-height: 1.6;" markdown="1">

## Education
*   **MBZUAI**: MSc in Natural Language Processing (Thesis: "**On Why Form Shapes Reason**")
*   **École 42**: Computer Science (**Systems-level software engineering**)
*   **Stanford University**: MS in Financial Mathematics
*   **Waseda University**: MBA
*   **MIT**: BS in Finance

</div>

---

## Research Experience
*   **Latent Program Networks (LPNs)**
    *   Introduced a framework for structuring latent transformations using algebraic constraints (**Associativity, Identity, Closure**).
    *   Demonstrated **systematic multi-step generalization** from single-step supervision without explicit symbolic rules or test-time search.
    *   Established that complex reasoning emerges when latent geometry is appropriately shaped, rather than through scale alone.
*   **G42 Group, Abu Dhabi**
    *   Designed and built an AI prototype for a **sovereign wealth fund**.
    *   Integrated LLMs, Retrieval-Augmented Generation (RAG), and agentic workflows to automate reasoning in fixed-income research and investment workflows.

---

## Methodological Research
### Agentic Reasoning Method
Reframes reasoning and planning as an emergent property of **modular, state-driven systems**. Decomposes cognition into interacting agents—each responsible for a specific role (generation, evaluation, refinement, integration)—governed by **persistent state transitions**.
*   **Uncertainty-Driven Routing**: Scalable reasoning triggered by consistency thresholds.
*   **Diversified Context Expansion**: Coherent response selection via judge agents over parallel perspectives.
*   **Hierarchical Modular Reasoning**: Parent-child subgraphs managed by **StateGraph** for global integration.
*   *Outcome*: Reasoning emerges from coordination and persistence among agents, rather than deeper prompting alone.

### Double Machine Learning (DML) Method
Addresses **causal estimation with high-dimensional confounders** by explicitly orthogonalizing parameters of interest with respect to nuisance components.
*   **Nuisance Estimation**: High-dimensional ML models for `E[Y|Z]` and `E[X|Z]`.
*   **Orthogonalized Estimation**: Residualized regression satisfying **Neyman orthogonality** to eliminate bias from representational error.
*   **Cross-fitting**: Ensures independence between nuisance and parameter estimation for root-n consistency.

> [!IMPORTANT]
> **Conceptual Connection: Structure over Scale**
> Both methods share a unifying theme: **reasoning and inference are enforced through structure, not scale**. Agentic reasoning imposes *architectural structure* (modularity/routing) for stability, while DML imposes *statistical structure* (orthogonality/residualization) for causal stability. Correctness is a designed constraint on how components interact, not an implicit byproduct of data volume.

---

## Publications / Manuscripts
*   *On Why Form Shapes Reason: Category-theoretic formalization of compositional reasoning* (MSc Thesis, MBZUAI).
*   *Adaptive Inductive Priors for Compositional Extrapolation* (Drafting).
*   *Geometric Paths in Latent Spaces: Reasoning as Learned Dynamical Systems* (In Preparation).

---

## Technical Skills
*   **Machine Learning**: JAX, PyTorch, Turing (Julia), Bayesian Inference, Probabilistic Modeling, W&B.
*   **AI Architecture**: LangGraph, DSPy, Hugging Face, PEFT (QLoRA), RAG Pipelines, Multi-agent Systems.
*   **Engineering**: Python, C, Julia, Docker, Poetry, FastAPI, Streamlit, Flutter/Firebase.
*   **Finance & DeFi**: Solidity, Hardhat, Ethers.js, QuantConnect, Credit/Rates Arbitrage, Systematic Risk Management.

---

## Prior Professional Background
*   **Chao Family Office (2016 – Present)**: Independent portfolio management; combining traditional arbitrage with **AI-driven models**.
*   **LetterOne Group (London)**: Portfolio Manager for global macro and FX within a **$16 billion investment group**.
*   **KBC AIM / Natixis (New York/Paris/London)**: Systematic credit and convertible arbitrage; **quantitative signal design**.
*   **JP Morgan Chase**, **Morgan Stanley**, **Salomon Brothers** (Tokyo/HK/Singapore): Rates and Equity Derivatives trading.
