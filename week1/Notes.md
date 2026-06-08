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
