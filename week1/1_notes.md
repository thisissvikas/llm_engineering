Sure — let's build this up from scratch with simple analogies.

## What is an LLM actually doing?

At its core, an LLM (Large Language Model) is a very sophisticated **autocomplete**. Given some text, it predicts the most likely next word, then the next, and so on. The "transformer" is the specific design (architecture) that makes this prediction so powerful.Let's build this up from scratch, one idea at a time.

## Step 1: What is an LLM trying to do?

Imagine a friend who has read _almost everything on the internet_. If you say "The sky is..." they'll instinctively guess "blue" — not because they memorized that exact sentence, but because they've seen the pattern so many times.

That's essentially what an LLM does: it predicts **the most likely next word**, over and over, to generate text. The "transformer" is the specific design that makes this prediction so good.

## Step 2: Breaking a sentence into pieces (tokens)

Before the model can "think" about text, it chops it into small pieces called **tokens** — usually words or word-chunks. For example:

> "I love pizza" → `["I", "love", "piz", "za"]`

Each token then gets converted into a list of numbers (a **vector**) that represents its meaning. Think of it like a GPS coordinate — but instead of location on Earth, it's a "location" in meaning-space, where similar words end up near each other.

## Step 3: The key idea — attention

Here's the breakthrough that makes transformers special: **every word looks at every other word in the sentence and decides how relevant it is.**

Take the sentence: _"The cat sat on the mat because it was tired."_

When the model processes the word **"it"**, how does it know "it" refers to the _cat_ and not the _mat_? It does this by having "it" look at every other word and assign an **attention score** — basically a relevance weight. "Cat" gets a high score, "mat" gets a low one.This is called **self-attention**, and it happens for _every_ word, not just "it" — each word is constantly re-evaluating how much it should care about every other word. This is what lets the model understand context, grammar, and meaning all at once, instead of reading word-by-word in isolation like older AI models did.

## Step 4: Doing this many times, from many angles

The model doesn't just do this attention trick once — it does it **many times in parallel**, each time focusing on a different kind of relationship (one might track grammar, another might track "who is doing what to whom"). These are called **attention heads**. Their combined insights are then blended together.

## Step 5: Stacking many layers

One round of "look around and update your understanding" isn't enough for deep comprehension. So this whole process — attention, then a bit of extra number-crunching (called a **feed-forward step**) — is stacked **dozens of times**, like layers of a cake. Each layer refines the understanding a little further, building from simple patterns (grammar) to complex ones (tone, reasoning, intent).

## Step 6: Predicting the next word

After passing through all the layers, the model has a rich understanding of the entire sentence so far. The very last step converts this understanding into a **probability for every possible next word** — and it picks one (usually the most likely, sometimes with a bit of randomness for creativity). Then the whole process repeats for the _next_ word, and the next, until the response is complete.

## Putting it all together

| Step         | What happens                         | Analogy                                                   |
| ------------ | ------------------------------------ | --------------------------------------------------------- |
| Tokenize     | Split text into pieces               | Chopping a sentence into puzzle pieces                    |
| Embed        | Turn pieces into numbers             | Giving each piece GPS coordinates in "meaning space"      |
| Attention    | Words check relevance of other words | Everyone in a room briefly checking who's talking to whom |
| Stack layers | Repeat and refine understanding      | Editing a draft multiple times, each pass adding polish   |
| Predict      | Guess the next word                  | Finishing someone's sentence based on context             |

That's the core idea — the "magic" of transformers isn't really magic, it's **attention done at massive scale**, repeated many times, trained on huge amounts of text.

# Lecture Notes: Introduction to LLMs and Inference

## 1. Closed-Source vs. Open-Source Models

The AI landscape is divided into two primary categories based on how the models are hosted, built, and accessed.

### Closed-Source (Frontier Models)

- **Definition:** Enormous models built by major labs spending hundreds of millions of dollars on training and infrastructure.
- **Characteristics:** You must pay to use them via a user interface or API; the weights and training datasets are hidden.
- **The "Big Four" Frontier Labs & Models:**
- **OpenAI (GPT):** The most famous AI lab. Changed the industry with ChatGPT (GPT-3.5) in Nov 2022. Current state-of-the-art includes **GPT-5**, which converges the standard GPT line with the reasoning "o" series ($o1, o3, o4\text{-mini}$).
- **Anthropic (Claude):** Founded by OpenAI alumni. Features three primary size tiers: **Haiku** (small/fast), **Sonnet** (middle/most powerful, e.g., _Claude 4.5 Sonnet_), and **Opus** (large). Powers the _Claude Code_ agent platform.
- **Google (Gemini):** Started far behind ("Bard" was an early failure) but caught up completely. Current state is _Gemini 2.5 Pro_, with _Gemini 3_ highly anticipated. Google often provides free/cheap tiers for students.
- **X.ai / Elon Musk (Grok):** Spelled with a **'k'**. The fourth major player in the top tier.

- **Other Notable Closed Models:** Perplexity, Cohere, and Mistral's commercial offerings.

### Open-Source (Open-Weight Models)

- **The "Open Weight" Distinction:** True open source implies sharing data and methodologies. Companies like Meta release the **model weights** (the learned parameters), meaning you can download and run them locally, though the training data remains private.
- **Key Players & Models:**
- **Meta (Llama):** Pioneered the open-weight strategy to capture market share. **Llama 4** is the massive flagship, while **Llama 3.2** offers highly efficient $1\text{B}$ and $3\text{B}$ parameter variations tailored for local computer execution.
- **Mistral AI:** A French company famous for **Mixtral**, a _Mixture of Experts (MoE)_ architecture that routes tasks to smaller, specialized sub-models.
- **Alibaba Cloud (Qwen):** A remarkably powerful, highly capable Chinese model series that remains somewhat underrated.
- **Google (Gemma):** The open-weight cousin to Gemini. Features an ultra-small $270\text{M}$ parameter model designed for basic local text generation.
- **Microsoft (Phi):** Currently at _Phi-4_, highly regarded for tool calling and enterprise/commercial workflows.

---

## 2. Deep Dive: DeepSeek, GPT-OS, & Model Distillation

### DeepSeek ($671\text{B}$ Parameters)

Took the industry by storm by delivering frontier-level capabilities at a fraction of the cost. While OpenAI spent over $100M+ to train its models, DeepSeek achieved similar performance for roughly **$4M**.

### Model Distillation

To create smaller, local versions of DeepSeek, the team did not train them from scratch. Instead, they used a process called **distillation**:

1. They took existing small, open-source models (specifically **Llama** and **Qwen**).
2. They used the massive $671\text{B}$ DeepSeek model to generate massive amounts of high-quality **synthetic (fake) data**.
3. They used this synthetic data to fine-tune and "educate" the smaller Llama/Qwen models.

### OpenAI’s Response: GPT-OS

In response to DeepSeek's open-source pressure, OpenAI released **GPT-OS**. It comes in a accessible $120\text{B}$ parameter version (runnable on high-end hardware) and a much larger tier.

---

## 3. The Three Ways to Consume Models

| Method                   | Description                                                                                                                  | Key Examples                                                                              |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| **1. Packaged Products** | Using an end-user application wrapped in a UI with added features (memory, web search, tools) managed by software engineers. | ChatGPT, Claude.ai                                                                        |
| **2. Cloud APIs**        | Making direct programmatic calls to hosting providers, hyperscalers, or smart routers in the cloud.                          | OpenAI API, Amazon Bedrock, Google Vertex AI, Azure ML, OpenRouter, **Groq (with a 'Q')** |
| **3. Direct Inference**  | Downloading model weights and executing them directly on local machine hardware.                                             | Ollama, Hugging Face Transformers                                                         |

> ⚠️ **Naming Confusion:** Do not confuse Elon Musk’s **Grok (with a 'K')**—which is an LLM—with **Groq (with a 'Q')**, which is a specialized cloud infrastructure provider designed to run open-source models incredibly fast using proprietary hardware.

---

## 4. Local Execution: Ollama vs. Hugging Face

When running open-weight models locally, developers generally choose between two tools:

### Hugging Face Transformers Library

- **How it works:** Pure data science workflow. You write Python/C++ code to load a neural network and execute the billions of mathematical weights inside your local Python interpreter.
- **Best for:** Highly customizable, programmatic development.

### Ollama

- **How it works:** A packaged application optimized in highly efficient C++. It compresses model weights into a single, specialized file format called a **GGUF** file.
- **Outcome:** It runs silently in the background and exposes a **local API server** on your machine ($localhost$), giving you the feel of a cloud API without sending data to the internet.

---

## 5. Key Glossary Term

- **Inference:** A technical term for _running_ an AI model. It is the process of passing a new input (prompt) into a trained neural network to compute and generate an output.

Here is a highly condensed, quick-reference summary of the lecture notes:

# Lecture Notes: LLM Types & Frontier Models

### Core Concepts

- **The Big Picture:** AI models have transitioned from basic text predictors to highly conversational, analytical assistants. However, they lack macro-level judgment and optimize for _plausibility_, meaning they require human oversight.

---

### The Three Breeds of LLMs

1. **Base Models:**

- _What they do:_ Predict the next most likely word in a text sequence (like smartphone predictive text).
- _Best use:_ The foundational starting point for training a model on entirely new skills.

2. **Chat / Instruct Models:**

- _What they do:_ Trained via **RLHF** (Reinforcement Learning from Human Feedback) to follow conversational structures using _System Prompts_, _User Prompts_, and _Assistant Replies_.
- _Best use:_ Fast, interactive communication and fluid content generation (e.g., writing emails).

3. **Reasoning / Thinking Models:**

- _What they do:_ Trained to output a step-by-step "thinking trace" before delivering an answer (born from the "Chain of Thought" prompt trick).
- _Best use:_ Complex logic, math, and heavy troubleshooting.

> **Hybrid Models:** Modern frontier models (e.g., GPT-5, Gemini 2.5) are hybrids. They dynamically adjust their **reasoning budget**. They skip thinking for a simple _"Hello,"_ but pause to think deeply when handed a complex riddle.
> **Budget Forcing (The S1 Paper):** A 2025 discovery showed that simply injecting the word _"Wait"_ into a model's internal thinking trace forces it to pause, self-reflect, and correct its own analytical mistakes.

---

### The Frontier Model Landscape

- **OpenAI:** GPT-5 (hybrid), O-series (pure reasoning), and GPT 4.1 (preferred by the lecturer for fast, interactive chat).
- **Anthropic:** Claude (Haiku, Sonnet 4.5, and Opus).
- **Google:** Gemini (v2.5 hybrid, v3 anticipated).
- **x.AI:** Grok.
- **DeepSeek:** Disrupted the industry by open-sourcing its most powerful models (prompting OpenAI to release its own open-source model, OSS).

---

### Strengths vs. Risks

#### The Good

- Incredibly strong at summarizing text, synthesizing data, and creating project frameworks.
- Has heavily supplanted platforms like Stack Overflow for writing and debugging code.

#### The Bad & Dangerous

- **Knowledge Cutoffs:** The core model cannot see past its training date. Extra wrapping code handles live web searches, not the LLM itself.
- **Confident Hallucinations:** Because they are trained to sound authoritative, they will state completely false information with absolute conviction.
- **The "Band-Aid" Bias:** They try to fix local errors by adding layers of complexity rather than stepping back to see if the entire premise is wrong.

---

### 💡 Golden Rule of LLMs

> Treat an LLM like a **tireless, brilliant, but blind junior analyst**. They will do immense amounts of work for you, but you must strictly supervise them, check their work, and keep them on the rails so they don't confidently lead you down a rabbit hole.

# Lecture Notes: LLM & Transformers

## What is GPT?

**G**enerative **P**re-trained **T**ransformer. Generates tokens (predicts what comes next), trained on massive internet-scraped data, built on the transformer architecture.

## The Transformer Architecture

### Origin: "Attention is All You Need" (2017)

A landmark Google paper describing a new neural network architecture. The authors didn't realize the magnitude of their discovery — they saw it as an optimization, not a revolution.

### Background: Neural Networks

- Traditional ML models: 20–200 parameters, statistical predictions from structured data
- Neural networks (1950s concept): many interconnected "artificial neurons," loosely inspired by the brain
- Deep learning: stacking more layers → deeper networks → better pattern recognition

### What Made Transformers Special

The transformer introduced a **self-attention layer** — a mechanism that figures out which parts of an input sequence matter most. Key advantages over predecessors like LSTMs:

- Massively parallelizable (LSTMs had to process step-by-step)
- Scales efficiently with more data and parameters
- Simpler than LSTMs, yet more practically powerful due to scalability

The LSTM was arguably more theoretically expressive, but couldn't be parallelized efficiently — the transformer's simplicity was its superpower.

### GPT Timeline

| Year | Model             | Notes                                 |
| ---- | ----------------- | ------------------------------------- |
| 2018 | GPT-1             | 117M parameters, basic                |
| 2019 | GPT-2             | 1.5B parameters                       |
| 2020 | GPT-3             | 175B parameters                       |
| 2022 | ChatGPT (GPT-3.5) | Added RLHF for chat-style interaction |
| 2023 | GPT-4             | ~1.76T parameters                     |
| 2024 | GPT-4o            | Multimodal                            |

### Transformer Not Fundamental — Just Efficient

Alternative architectures exist (state space, hybrid). None have definitively beaten transformers yet, but the transformer isn't considered the only possible path — it's the most efficient one found so far.

---

## Emergent Intelligence

A surprising byproduct of scale: large enough models don't just produce _plausible_ tokens — they produce _accurate_, _intelligent_ ones. Predicting realistic text is expected; actually solving math problems correctly is not. This property — **emergent intelligence** — is still not fully understood.

---

## Parameters

The "weights" a model learns during training. More parameters → more capacity to absorb training data.

| Model                     | Parameters               |
| ------------------------- | ------------------------ |
| GPT-1                     | 117M                     |
| GPT-2                     | 1.5B                     |
| GPT-3                     | 175B                     |
| GPT-4                     | ~1.76T                   |
| DeepSeek                  | 671B                     |
| Frontier models (current) | Likely tens of trillions |

Efficiency has improved — Gemma at 270M parameters outperforms GPT-2 at 1.5B. Model "flavors" (e.g., Claude Haiku / Sonnet / Opus) represent small, medium, and large parameter counts.

### Training Time vs. Inference Time Scaling

- **Training time scaling**: bigger model, more parameters, more training data
- **Inference time scaling**: getting more out of a model at runtime — e.g., chain-of-thought reasoning, RAG, richer context (prompt engineering / context engineering)

Both tracks are now actively pursued in parallel.

---

## Tokens

The unit of input/output for LLMs — not characters, not full words, but _chunks_ (word fragments, whole words, or common word pairs).

### Why Tokens?

- Characters: too granular, network had to learn too much
- Full words: vocabulary explodes with proper nouns and rare words
- Tokens: efficient middle ground — limited vocab, captures meaningful word fragments

### Token Rules of Thumb

- ~4 characters per token
- ~750 words per 1,000 tokens
- Complete Works of Shakespeare ≈ 1.2M tokens
- Code is denser — closer to 1 token per character

Tokens include a "beginning of word" marker — `important` (start of word) and `crafted` (word ending) are treated differently.

---

## Context Window

The maximum number of tokens a model can consider at once — includes the **entire conversation history**, not just the latest message. Output tokens are also generated one at a time, fed back into the input.

| Model               | Context Window |
| ------------------- | -------------- |
| GPT-5               | 400K tokens    |
| Claude (range)      | 200K tokens    |
| GPT-o (open source) | ~130K tokens   |
| Gemini 2.5 Flash    | 1M tokens      |

Relevant for techniques like multi-shot prompting and RAG, both of which rely heavily on context window capacity.

---

## API Costs

Charged per **input tokens** (full conversation history) and **output tokens** (including hidden reasoning traces for reasoning models).

Example pricing (GPT-5):

- Input: $1.25 / 1M tokens
- Output: $10 / 1M tokens
- GPT-5 Nano output: $0.40 / 1M tokens

**Caching**: sending the same inputs repeatedly within a short window is cheaper. Claude supports explicit prompt caching; GPT does it automatically.

Useful reference: [Vellum Leaderboard](https://www.vellum.ai/llm-leaderboard) — compares context windows and API costs across major models.

---

## Evolution of the Field

- **Prompt engineering** → briefly a high-paying job title, now universal skill
- **Copilots** (GitHub, Microsoft) → human-AI collaboration on documents and code
- **Context engineering** → the new prompt engineering; thinking holistically about what information to give the model, including tools and structured data
- **Agentic AI** → LLM in a loop with tools, controlling its own workflow; exemplified by Claude Code

# The Transformer Architecture

The transformer is the foundation of modern LLMs (GPT, Claude, etc.), introduced in _"Attention Is All You Need"_ (2017). It processes sequences using **self-attention** instead of recurrence, allowing full parallelization.

## Core Components

**1. Input Embeddings**
Tokens (words/subwords) are converted into dense vectors, combined with **positional encodings** since transformers have no inherent sense of order.

**2. Self-Attention**
Each token computes three vectors — **Query (Q)**, **Key (K)**, **Value (V)** — and attends to all other tokens to determine relevance:

```
Attention(Q, K, V) = softmax(QKᵀ / √d_k) V
```

This lets the model weigh how much each word should "focus on" every other word in the sequence.

**3. Multi-Head Attention**
Multiple attention operations run in parallel ("heads"), each learning different relationships (syntax, semantics, coreference, etc.), then their outputs are concatenated.

**4. Feed-Forward Network (FFN)**
Each token's representation passes through a position-wise fully connected network, adding non-linear transformation capacity.

**5. Residual Connections + Layer Normalization**
Each sub-layer (attention, FFN) has a skip connection and normalization, stabilizing training in deep networks.

**6. Stacking Layers**
These blocks (attention → FFN) are stacked N times (e.g., 32, 96 layers) to build depth and representational power.

## Architecture Variants

| Type            | Description                | Example Use                       |
| --------------- | -------------------------- | --------------------------------- |
| Encoder-only    | Bidirectional attention    | BERT (classification, embeddings) |
| Decoder-only    | Causal (masked) attention  | GPT, Claude (text generation)     |
| Encoder-Decoder | Both, with cross-attention | T5, translation                   |

Most modern LLMs use a **decoder-only** architecture: each token can only attend to previous tokens (causal masking), enabling autoregressive next-token prediction.

## Output

The final layer projects hidden states to a probability distribution over the vocabulary, from which the next token is sampled.
