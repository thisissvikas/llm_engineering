# Lecture 1: Choosing the Right LLM — Model Selection Strategy & Basics

**Core Principle**

- There is no universally "best" LLM.
- **Key Question:** What is the right model for the specific task and business constraints?
- **Process:** Define requirements $\rightarrow$ Filter candidate set via basic specs $\rightarrow$ Evaluate against benchmarks/arenas.

---

**Model Card Specifications**

- **Type:**

  - _Open vs. Closed Source:_ Self-hosted control vs. managed cloud API.
  - _Chat vs. Reasoning vs. Hybrid:_ Reasoning models excel at logic via test-time compute but suffer from higher latency and lower creative capability; hybrid models toggle based on context.

- **Knowledge Cutoff:** Determines native factual knowledge vs. need for inference-time tools/RAG.
- **Parameters & Training Tokens:** Proxy for capability and knowledge density (larger $\approx$ smarter, though small models are increasingly efficient).
- **Context Window:** Max token capacity for full history, retrieved context, system instructions, and output.

---

**Operational & Business Criteria**

| Criteria              | Core Consideration                                                                                                            |
| --------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| **Total Cost**        | API pricing vs. local/cloud compute, custom fine-tuning, and RAG build costs.                                                 |
| **Time to Market**    | Frontier APIs enable fast MVP delivery; smaller/local models lower runtime costs but increase R&D effort.                     |
| **Rate Limits**       | RPM/TPM provider caps; often requires enterprise tiers or fallback routing.                                                   |
| **Speed vs. Latency** | Generation throughput (tokens/sec) vs. **Time to First Token (TTFT)** (critical for streaming UIs; high in reasoning models). |
| **Licensing**         | Commercial use limits, revenue caps, or competition restrictions on open-weights models.                                      |

# Lecture 2: The Chinchilla Scaling Law — Parameters, Training Data, & Scaling Limits

**Core Definition & Origin**

- **Origin:** Introduced by DeepMind/Google via the **Chinchilla** model (successor to Gopher).
- **The Law:** Model parameters and training tokens should scale **in equal proportion (1:1)** for optimal compute efficiency.
- **Core Rule of Thumb:** If you double the parameter count ($2\times$), you must double the training dataset size ($2\times$ tokens) to realize the full performance gain—and vice versa.
- **Diminishing Returns Indicator:** If training performance plateaus before consuming the dataset, the parameter capacity is saturated, signaling the need for a larger model.

---

**Why Chinchilla is Less Emphasized Today**

1. **Parameter Efficiency & Compression:**

   - Modern architectures, improved data filtering/curation, and post-training compression (e.g., pruning, distillation) pack significantly more capability into smaller parameter footprints (e.g., Llama 3.2, Gemma compact variants).

2. **Shift to Inference-Time Compute:**

   - Performance gains no longer rely solely on pre-training scale.
   - Test-time techniques (chain-of-thought reasoning, search/planning, tool use, RAG) yield major capability boosts without retraining larger base models.

---

**Summary Takeaway**

- _Ceteris paribus_ (holding architecture and training techniques constant): **Double the parameters $\rightarrow$ double the training tokens.**
- Today, it serves as a baseline foundational rule rather than a strict boundary for model design.

# Lecture 3: Understanding AI Model Benchmarks (GPQA, MMLU-Pro, HLE & Beyond)

**Core Role of Benchmarks**

- **Purpose:** Serves as the initial quantitative filter to narrow down candidate models, though not the final validation for production suitability.
- **Key Focus:** Differentiating top frontier and reasoning models requires **high-difficulty benchmarks** that resist saturation and contamination.

---

**The 6 Hard Benchmarks Breakdown**

| Benchmark                                                       | Focus / Domain                               | Format & Complexity                                                                            | Key Baseline & Characteristics                                                                              |
| --------------------------------------------------------------- | -------------------------------------------- | ---------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| **GPQA** _(Google-Proof Q&A)_                                   | Hard Sciences (Physics, Chemistry, Biology)  | 448 expert-level questions designed to resist simple search retrieval.                         | • Non-PhD humans w/ Google (30 min): **34%**.<br>• PhD-level humans: **65%**.<br>• GPT-4 baseline: **39%**. |
| **MMLU-Pro** _(Massive Multitask Language Understanding - Pro)_ | Multitask Reasoning & Language Understanding | Replaced flawed MMLU (4 choices, easy/noisy) with **10 choices** and removed ambiguity.        | Robust, reasoning-heavy discriminator that significantly reduces lucky guesses.                             |
| **AIME** _(American Invitational Mathematics Examination)_      | Advanced Mathematics & Competition Logic     | High-school Olympiad-level competitive math puzzles (not basic mental math).                   | Stresses deep multi-step mathematical and algorithmic reasoning.                                            |
| **LiveCodeBench**                                               | Real-time Code Generation                    | Competitive programming problems harvested from LeetCode, Codeforces, etc.                     | Continuously updated with fresh, post-cutoff problems to **prevent training data contamination**.           |
| **MuSR** _(Multistep Soft Reasoning)_                           | Complex Soft & Multi-step Reasoning          | Includes ~1,000-word murder mysteries ("whodunits") evaluating means, motive, and opportunity. | Tests cohesive narrative comprehension and deductive inference over long contexts.                          |
| **HLE** _(Humanity's Last Exam)_                                | Extreme / Superhuman General Intelligence    | 2,500 highly complex, multi-domain edge questions built to prevent quick model saturation.     | Top frontier models initially scored only **2%–3%** upon late-2024 launch.                                  |

---

**Summary Takeaways**

- Older benchmarks (like original MMLU) saturated quickly; modern evaluations require **reasoning-intensive, contamination-free, and moving-target datasets**.
- Frontier model evaluation is shifting from static knowledge recall to **deep reasoning (MuSR, AIME), live coding (LiveCodeBench), and ultra-hard multi-disciplinary problem solving (GPQA, HLE)**.

# Lecture 4: Limitations of AI Benchmarks — Data Contamination & Overfitting

**Core Principle**

- Benchmarks provide initial directional indicators, not conclusive proof of real-world production suitability.
- High benchmark scores can mask critical flaws due to data leakage, narrow test designs, and artificial optimization.

---

**Key Failure Modes & Limitations**

| Limitation                                   | Mechanism                                                                                                  | Real-World Impact / Evidence                                                                                                                                                 |
| -------------------------------------------- | ---------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Training Data Contamination**              | Test questions and answers leak into the model's pre-training web corpus.                                  | Models memorize answers rather than reasoning.<br>• _Apple's GSM-Symbolic Paper:_ Altering surface names/numbers without changing math logic caused sharp performance drops. |
| **Benchmark Inconsistency & Self-Reporting** | Hardware differences, prompt variations, and self-reported provider numbers lack standardized replication. | Results between providers often cannot be compared apples-to-apples due to undisclosed execution setups.                                                                     |
| **Narrow Scope vs. General Ability**         | Tests isolate specific domains (e.g., GPQA on hard sciences) rather than broad multimodal/spatial logic.   | A model scoring "PhD level" on GPQA can still fail at basic spatial tasks (e.g., visualizing a chessboard one move from checkmate).                                          |
| **Format Constraints & Nuance Loss**         | Heavy reliance on multiple-choice or deterministic structures (e.g., identifying suspect in MuSR).         | Inability to measure edge-case handling, ambiguous requirements, or nuanced conversational depth.                                                                            |
| **Saturation**                               | Rapid model progress leads to ceiling effects ($>99\%$ accuracy).                                          | Older foundational benchmarks quickly become obsolete and fail to differentiate top models.                                                                                  |
| **Metric Overfitting (Selection Bias)**      | Iteratively picking candidate checkpoints based solely on target benchmark scores.                         | Implicitly tunes the model to the test set; performance collapses under slight question perturbations.                                                                       |
| **Evaluation Awareness (Emergent Risk)**     | Advanced frontier models detecting when they are in an evaluation environment vs. deployment.              | Risk of "faking" alignment, safety compliance, or instruction-following during testing while behaving differently in production.                                             |

---

**Summary Takeaways**

- **Mitigations:** Use dynamic benchmarks with rotating test sets (e.g., LiveCodeBench), template-based evaluations (e.g., GSM-Symbolic), and private holdout sets.
- Treat public benchmark leaderboards as an initial coarse filter, followed by domain-specific task evaluation.

# Lecture 5: Navigating AI Leaderboards (Artificial Analysis, HuggingFace & More)

**Core Role of Leaderboards**

- Leaderboards aggregate standardized benchmarks, cost metrics, throughput, and head-to-head comparisons to help developers identify Pareto-optimal models.
- Real-time tracking is necessary because frontier rankings shift rapidly with new releases and post-training updates.

---

**Top 5 Leaderboards Overview**

| Platform                | Specialization / Primary Strength                                                                                      |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| **Artificial Analysis** | Comprehensive, independent hub evaluating models across **Intelligence, Speed (throughput/TTFT), and Price**.          |
| **Vellum**              | Excellent for direct side-by-side **API pricing** and **context window** comparisons across major providers.           |
| **Scale AI (SEAL)**     | Highly specialized, expert-curated leaderboards focused on rigorous frontier evaluation domains.                       |
| **Hugging Face Spaces** | Hosts numerous domain-specific community spaces (legacy Open LLM Leaderboard is static/deprecated due to gaming).      |
| **LiveBench**           | Uses dynamically refreshed, non-static questions specifically engineered to **resist data contamination and leakage**. |

---

**Deep-Dive: Artificial Analysis Intelligence Index**

- **Methodology:** Aggregates composite hard benchmarks (e.g., MMLU-Pro, GPQA Diamond, LiveCodeBench, AIME, HLE) into a unified intelligence index.
- **Core Dimensions Tracked:**

  1. **Intelligence:** Aggregate benchmark problem-solving capacity.
  2. **Speed:** Throughput (tokens/sec) and Time to First Token (TTFT).
  3. **Price:** Cost per million input/output/cached tokens.

- **Historical Trend (2022–Present):**
  - Model capability curves continue steep upward trajectories without plateauing.
  - **Primary Driver Shift:** Early gains stemmed from _pre-training scale_ (Chinchilla-style scaling); modern frontier jumps are heavily driven by _inference-time compute_ (test-time reasoning, tool use, agentic loops, prompt optimization).

# Lecture 6: LM Arena — Blind Testing AI Models with Community Elo Ratings

**Core Definition & Mechanism**

- **Platform:** **LM Arena** (originally _LMSYS Chatbot Arena_).
- **Methodology:** Crowdsourced, **blind side-by-side (A/B) human evaluations**.

  - Users submit an arbitrary prompt to two anonymized models (Assistant A vs. Assistant B).
  - The user votes on the superior response before model identities are revealed.

- **Ranking Algorithm:** Uses a standard **Elo rating system** (adapted from chess and competitive gaming) to compute relative skill levels from head-to-head win/loss outcomes.

---

**Why LM Arena Matters**

| Strength                              | Core Value                                                                                                                |
| ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| **Bypasses Metric Gaming**            | Removes reliance on static, leaky multiple-choice benchmarks or automated LLM-as-a-judge biases.                          |
| **Captures True Human Preference**    | Measures open-ended conversational nuance, style, coherence, and helpfulness that automated tests miss.                   |
| **Continuous Real-Time Ground Truth** | Serves as the community gold standard for how frontier and open-source models perform in actual user-facing interactions. |

---

**Key Takeaways**

- Specialized domain sub-leaderboards exist (e.g., Text, Coding, Hard Prompts, Vision) alongside the overall leaderboard.
- Frontier tiers frequently cluster closely within statistical error margins, resulting in ties among top models (e.g., Gemini Pro, Claude Opus/Sonnet, GPT series).

# Lecture 7: Commercial Use Cases — Automation, Augmentation & Agentic AI

**The Business Value Continuum**

1. **Automation:** Eliminates manual, repetitive, error-prone tasks to drive operational efficiency.
2. **Augmentation:** Acts as a co-pilot alongside humans to multiply individual productivity and delegation.
3. **Differentiation:** Unlocks entirely new business capabilities, products, and operational models previously impossible without AI.

---

**3 Architectures of Commercial AI Solutions**

| Solution Tier                   | Architecture & Definition                                                                                   | Industry Examples                                                                                                                                                                                                                                                      |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1. ChatGPT Wrappers**         | Thin application layers that pass user context to frontier model APIs out-of-the-box.                       | Standard mobile GenAI integrations, baseline coding/writing copilots (e.g., standard Duolingo AI features).                                                                                                                                                            |
| **2. Bespoke Domain Platforms** | Proprietary architectures combining specialized data, fine-tuned models, RAG, and integrated tool chains.   | • **Harvey:** Legal workflows and contract analysis.<br>• **Khanmigo (Khan Academy):** Socratic educational tutoring.<br>• **Nebula:** Talent matching & career intelligence.<br>• **Salesforce Health / Palantir:** Enterprise data systems & clinical documentation. |
| **3. Agentic AI Systems**       | Autonomous multi-step software capable of reasoning, decision-making, tool execution, and goal achievement. | OpenAI Codex/Agent mode, Claude Code, and enterprise-grade autonomous decision engines.                                                                                                                                                                                |

---

**Core Strategic Moat: Proprietary Data**

- _Andrew Ng quote:_ _"AI is the new electricity."_
- _Strategic Corollary:_ **Proprietary Data is the fuel/electricity generation source.**
- Unique, high-quality private data + inference-time systems (RAG, tools) create defensible commercial moats that generic frontier APIs cannot replicate.

---

**Upcoming Hands-on Focus**

- Practical evaluation of LLMs for specialized tasks via a **Python $\rightarrow$ C++ high-performance transpilation** pipeline.

# Lecture 8: Model Evaluation — Technical Metrics vs. Business Outcomes

**The Core Sub-Question of LLM Engineering**

- Model selection is fundamentally bounded by evaluation: _"How do you quantify and validate that Solution A is superior to Solution B?"_
- Effective engineering requires balancing **model-centric technical metrics** with **business-centric outcome metrics**.

---

### Technical Metrics vs. Business Outcome Metrics

| Dimension            | Model-Centric (Technical) Metrics                                                                                                                                                                                                                                                                     | Business-Centric (Outcome) Metrics                                                                                                                                                            |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Definition**       | Mathematical loss and performance scores computed directly from model outputs and logits.                                                                                                                                                                                                             | Commercial KPIs and user signals measuring real-world business value.                                                                                                                         |
| **Primary Examples** | • **Cross-Entropy Loss:** Core objective for LLM training; lower is better (0 is perfect).<br>• **Perplexity:** Uncertainty for next-token prediction ($1$ is perfect certainty; $100$ means $100$ equally likely possibilities).<br>• **Traditional ML:** MSE, Precision, Recall, F1-Score, AUC-ROC. | • **User Signals:** Customer satisfaction (CSAT), thumbs up/down ratings.<br>• **Financials:** Revenue growth, ROI, cost savings.<br>• **Conversion:** Task completion rates, user retention. |
| **Advantages**       | • Fast, deterministic, directly computable.<br>• Usable directly for training loops and backpropagation.                                                                                                                                                                                              | • Measures the true commercial objective.<br>• Reflects real end-user experience.                                                                                                             |
| **Limitations**      | Low loss or high AUC does not guarantee customer satisfaction or business viability.                                                                                                                                                                                                                  | • **High Noise & Latency:** Influenced by UI, market, or external factors.<br>• Cannot be used for real-time model optimization.                                                              |

---

**Bridging the Gap: The Engineer's Superpower**

- **The "Leap of Faith":** Technical optimizations (e.g., lower perplexity) do not automatically improve commercial outcomes due to intermediate noise (UX friction, product mismatch).
- **Core Takeaway:** AI engineers optimize technical loss functions, but the highest-leverage skill is ensuring those improvements **reliably map to measurable business KPIs**.
