# Lecture 1: Introduction to RAG (Retrieval Augmented Generation)

## What is RAG?

- Stands for **Retrieval Augmented Generation**
- Core idea is simple — you've probably already thought of it without knowing the name
- Builds on inference-time prompting techniques already covered:
  - **Multi-shot prompting** — example Q&As in context
  - **Tools** — e.g., fetching ticket prices
  - General principle: _more relevant context in the prompt → better/more "expert" LLM responses_

### The Small Idea Behind RAG

1. Build a **knowledge base** (just a database of useful info — e.g., ticket prices, activities for an airline).
2. When a user asks a question, **query the database** for relevant info (e.g., simple string match — does the question mention "London"?).
3. **Inject retrieved info into the prompt** as background context.
4. LLM generates an answer — biased toward using the relevant injected context (since it predicts tokens coherent with the prompt).
5. Irrelevant retrieved info doesn't hurt — the LLM can ignore what's not needed.

### Basic RAG Flow

```
User question → Code → Query Knowledge Base (DB) → Relevant info retrieved
→ Combined with question into prompt → Sent to LLM → Answer → User
```

---

## Worked Example: "InsureLLM"

- Fictitious insurance-tech startup
- **Knowledge base** = simulated company share drive (docs about the company)
- **Goal**: Build a knowledge worker AI that can answer expert questions about the company

### First (Naive) Approach — "Blunt Instrument"

- Read files in the share drive
- Extract names of **products** and **employees**
- If a user's question contains one of these names → **shove the entire matching document** into the prompt
- Simple, janky, but a starting point — will test if it's "good enough" before building something more advanced

---

## Key Takeaway

RAG's core concept is not complex: **retrieve relevant context → insert into prompt → let the LLM answer using it.** The rest of the course builds progressively more sophisticated retrieval methods on top of this basic idea.

# Lecture 2: Vector Embeddings and Encoder LLMs — The Foundation of RAG

## The Big Idea Behind RAG

- Vectors are what enable **fuzzy lookup**: given a user's question, finding _relevant_ context even without exact matches.
- This is the core mechanism that makes RAG powerful (beyond the "small idea" of simple string matching from Lecture 1).

---

## Two Types of LLMs

### 1. Autoregressive LLMs (what we've used so far: GPT, Claude, Gemini, DeepSeek, Grok)

- Trained to take an input sequence and **predict the next token**
- Generates one token at a time, feeding output back in as input, repeating
- The default/most common type — if someone says "LLM" with no context, this is what they mean

### 2. Autoencoding LLMs (a.k.a. **Encoders** / **Embedding Models**)

- Takes a **full input sequence** and produces **one output** representing the entire input (not "what comes next")
- **Use cases**:
  - Classification (e.g., sentiment analysis — happy/sad, positive/negative)
  - Topic/category detection
  - **Producing vector embeddings** ← the key application for RAG

---

## Vector Embeddings

- An encoder model converts input tokens → a **set of numbers** (e.g., 10 numbers) that represent the **meaning** of the input
- This set of numbers = a **vector** (a point in n-dimensional space)
  - 3 numbers → x, y, z point in 3D space
  - 10 numbers → point in "10-dimensional space" (same idea, just more dimensions)
- This process of mapping text → numbers representing meaning = **vector embedding**

### Notable Embedding Models

| Model                 | Notes                                                                                                     |
| --------------------- | --------------------------------------------------------------------------------------------------------- |
| **BERT**              | Created by Google, 2018. Bidirectional Encoder Representations from Transformers. Predates the term "LLM" |
| **OpenAI Embeddings** | e.g., `text-embedding-3-large`, `text-embedding-3-small`                                                  |
| **all-MiniLM-L6-v2**  | Popular open-source embedding model (will be used in course)                                              |

---

## Tokens vs. Vectors (Clearing Up Confusion)

|                   | Tokens                                                                  | Vectors                                           |
| ----------------- | ----------------------------------------------------------------------- | ------------------------------------------------- |
| **Role**          | Inputs                                                                  | Outputs                                           |
| **What they are** | Numeric representation of text (words/word-fragments mapped to numbers) | Numbers representing the _meaning_ of an input    |
| **Purpose**       | Let models process text mathematically                                  | Reflect understanding, relationships, and meaning |

### Key Points

- Models can't process raw text — everything must first become numbers (**tokens**)
- Early tokenization attempts:
  - Whole words → problems with vocabulary size, proper nouns, etc.
  - Individual characters → too complex
  - **Solution**: sub-word fragments (covered in Week 1) — works well in practice
- Tokenizers are simple: text → tokens via mapping + rules
- **Flow**: Text → Tokens (input) → Model → Vector (output, reflects meaning)
- **Advanced/pro note**: Vectors also exist as _internal_ representations between layers inside a model — even autoregressive models convert tokens to vectors internally. Not essential to know for this course, but good to be aware of.

---

## Key Takeaway

Encoder/embedding models convert text into **vectors** — numeric representations of meaning. These vectors are what allow RAG systems to perform **similarity-based ("fuzzy") lookups** in a knowledge base, rather than relying on exact keyword matches.

# Lecture 3: How Vector Embeddings Represent Meaning — From word2vec to Encoders

## What Can Vectors Represent?

Vectors aren't limited to representing single words — they can represent meaning at many levels of granularity or abstraction:

- A character, a token, a word, a sentence, a paragraph, a whole document
- Abstract concepts too — e.g., job postings, career trajectories, opportunities (real example from the instructor's work at Nebula)

### Dimensionality

- Can be as few as 3 numbers (like an x, y, z point) or **hundreds/thousands of dimensions**
- A 1000-number vector = a point in "1000-dimensional space" — hard to visualize, but conceptually the same idea as 3D coordinates

---

## Core Principle: Proximity = Similarity

- **Points close to each other in vector space represent similar meanings** — even if the actual words are completely different
- Example:
  - _"What are ticket prices to travel from New York to London?"_
  - _"What's the price of a flight from JFK to Heathrow Airport?"_
  - → Different words, same meaning → vectors close together in space
- Models are trained specifically to produce embeddings with this property

---

## The word2vec Discovery (Pre-Transformer NLP Era)

- **word2vec**: an earlier algorithm (predecessor to modern encoders) that mapped individual words to vectors
- Similar words cluster together in vector space (e.g., "orange" near "mandarin," "tangerine," other fruits/colors)

### The Famous "Vector Arithmetic" Result

You can **add and subtract vectors** to move between meanings:

```
king − man + woman ≈ queen
Paris − France + England ≈ London
```

- Subtracting a concept (e.g., "man") and adding another (e.g., "woman") moves you through the meaning-space
- The closest point to the resulting vector matches the expected word — a surprising, almost "spooky" result
- This works for single words with word2vec, and the same principle extends to **entire paragraphs** with modern encoder models — you can shift the meaning of a whole passage by adding/removing concepts, regardless of the original wording

---

## Key Technical Note

- Similarity between vectors isn't measured by simple distance alone — it typically uses **cosine similarity**
- This is a minor technicality for now (will be revisited later) — the important takeaway is: **similar numbers = similar concepts**

---

## Key Takeaway

Vector embeddings encode meaning such that:

1. **Semantically similar inputs produce nearby vectors** (regardless of exact wording)
2. **Vector arithmetic can shift meaning** — you can literally add/subtract concepts using vector math

This is the mathematical foundation that makes **fuzzy, meaning-based retrieval** possible in RAG systems.

Yes — this can be tightened quite a bit while keeping all the important concepts.

# Lecture 4: Understanding the Big Idea Behind RAG and Vector Data Stores

## The Core Idea Behind RAG

Traditional search often relies on **exact words**.

Example:

- User asks: _"What are ticket prices to Heathrow?"_
- Database contains: _"Ticket prices to London"_
- Exact matching may fail even though the meanings are related

RAG solves this using **semantic search** — searching by meaning rather than wording.

---

## How RAG Retrieval Works

1. The user's question is converted into a **vector embedding** using an embedding/encoder model.
2. Knowledge-base content has already been converted into vectors using the **same model**.
3. A **vector store** keeps the vectors along with their original text.
4. The system finds vectors closest to the question vector.
5. The associated text is retrieved and added to the LLM prompt.

```text
Question → Embedding → Vector Search → Relevant Text → LLM → Answer
```

Because similar meanings produce nearby vectors:

> _"Ticket prices to Heathrow"_ can retrieve _"Ticket prices to London"_ even without matching words.

This is **semantic or fuzzy retrieval**.

---

## Vector Store

A vector store essentially keeps:

```text
Vector → Original Text
```

It allows information to be searched based on **semantic similarity**.

---

## Important: Embedding Model ≠ Generative LLM

These models have different jobs:

- **Embedding/Encoder Model:** converts text → vectors and finds relevant content
- **Generative LLM:** receives natural-language context and generates the answer

**Vectors are not sent to the generative LLM.**

The LLM receives:

```text
User Question + Retrieved Natural-Language Context
```

---

## RAG Is Heuristic

Instead of putting the entire knowledge base into the context window, RAG retrieves only the information **most likely to be useful**.

Retrieval is not perfect — it is an **empirical process** involving experimentation and tuning to improve which context gets selected.

---

## Key Takeaway

RAG = **Retrieve relevant context + give it to an LLM for generation**

```text
Question
  ↓
Embedding Model
  ↓
Vector Similarity Search
  ↓
Relevant Text
  ↓
Question + Context → LLM → Answer
```

The embedding model handles **retrieval by meaning**; the LLM handles **answer generation**.

# Lecture 5: Vectors for RAG - Introduction to LangChain and Vector Databases

## RAG Recap

The core RAG flow is:

- Convert the user's question into a **vector embedding**
- Search a **vector store** for semantically similar content
- Retrieve the associated text
- Add that text to the LLM prompt as context

This replaces simple keyword matching with **semantic/fuzzy search**.

---

## What Is LangChain?

**LangChain** is an open-source framework for building applications with LLMs.

- Launched in **2022** by Harrison Chase
- Acts as an **abstraction layer** over different models and services
- Helps connect components into workflows such as:
  - RAG pipelines
  - Assistants
  - Summarization
  - Tool-using applications

LangChain 1.0 introduced a significant restructuring of the framework.

---

## Why Use LangChain?

### Advantages

- Makes it quick to **connect LLM components**
- Provides built-in integrations for models, tools, vector stores, prompts, etc.
- Can reduce RAG implementations to relatively little code
- Has significant **enterprise adoption**, making it useful technology to understand

### Downsides

- The need for model abstraction has reduced because many providers now expose **OpenAI-compatible APIs**
- Common patterns such as tool calling and prompt handling are increasingly standardized
- LangChain has grown from a lightweight library into a relatively **heavy framework**
- Comes with:
  - Significant terminology
  - Additional abstractions
  - A larger learning curve

Lighter alternatives such as **LiteLLM** may be enough when the main requirement is simply switching between model providers.

---

## LangChain Ecosystem

LangChain is part of a broader ecosystem:

- **LangChain** — framework for building LLM applications and pipelines
- **LangGraph** — orchestration of agents and workflows as graphs
- **LangSmith** — observability, tracing, and evaluation for LLM applications

---

## What We Will Use It For

For RAG, LangChain can help with the complete ingestion pipeline:

1. Load documents
2. Split documents into **chunks**
3. Convert chunks into **embeddings**
4. Store embeddings in a **vector database**
5. Search for relevant chunks when a user asks a question

The upcoming example uses **Chroma** as the vector store.

---

## Key Takeaway

LangChain provides convenient abstractions for building RAG pipelines quickly, but those abstractions come with additional complexity and learning overhead.

For RAG, its main value is simplifying the workflow:

**Documents → Chunks → Embeddings → Vector Store → Semantic Search → LLM Context**

# Lecture 6: Encoder Models vs Vector Databases — OpenAI, BERT, Chroma & FAISS

## Two Separate Steps

A RAG pipeline involves two different components:

1. **Encoder / Embedding Model** — converts text into a vector
2. **Vector Database / Store** — stores vectors and quickly finds similar ones

**Important:** The vector database does **not create embeddings**. It only stores and searches them.

---

## Encoder / Embedding Models

Encoders transform text into vectors representing its meaning.

Popular examples:

- **word2vec** — early word-level embeddings
- **BERT** — Google's influential transformer encoder released in 2018
- **OpenAI**
  - `text-embedding-3-small`
  - `text-embedding-3-large`
- **Google** — `Gemini Embedding 001`
- **Hugging Face Sentence Transformers**
  - `all-MiniLM-L6-v2` — widely used open-source embedding model

Different embedding models can produce very different retrieval quality, so **testing the encoder against your actual use case is important**.

---

## Document → Vector Pipeline

Typical RAG ingestion flow:

**Documents → Chunks → Embeddings → Vector Store**

Example chunking setup:

- ~1000 characters per chunk
- ~200 characters overlap
- Recursive splitting using paragraphs, line breaks, spaces, etc.

The encoder converts each chunk into a vector, which is then stored alongside the original text.

---

## Vector Stores

Vector stores are optimized for queries such as:

> "Find the vectors most similar to this vector."

Popular options include:

- **Chroma** — simple, open-source vector database
- **FAISS** — Meta's _Facebook AI Similarity Search_
  - Fast in-memory vector similarity search
  - More of a search library than a full database
- **Pinecone / managed vector databases** — hosted, scalable enterprise options

---

## Existing Databases Can Store Vectors Too

Dedicated vector databases are not always necessary.

Many mainstream systems now support vector search:

- **PostgreSQL**
- **MongoDB**
- **Elasticsearch**

This allows applications to combine traditional filtering/search with vector similarity in the same system.

---

## Encoder Choice vs Database Choice

### Encoder Choice

Primarily an **AI/retrieval-quality decision**:

- Does it understand your domain well?
- Does it retrieve the most relevant content?
- How well does it perform on your specific data?

This usually requires **experimentation and evaluation**.

### Vector Database Choice

Primarily an **infrastructure decision**:

- Cost
- Performance
- Scale
- Availability/resilience
- Backups
- Security
- Operational complexity

Most vector stores can work with embeddings produced by many different models.

---

## Key Takeaway

Do not confuse:

**Embedding Model → Creates vectors**

with:

**Vector Database → Stores and searches vectors**

For RAG quality, the **embedding model often matters more** because it determines how well semantic similarity reflects your actual business problem.

The vector database is usually chosen more like any other infrastructure component — based on **cost, performance, scalability, and operational requirements**.

# Lecture 7: Building a Complete RAG Pipeline with LangChain and Chroma

## Completing the RAG Pipeline

Previous step:

**Documents → Chunks → Embeddings → Chroma**

Now we complete the flow:

**User Question → Retrieve Relevant Chunks → Add Context to Prompt → LLM → Answer**

---

## RAG Flow Recap

1. User asks a question.
2. An **embedding model** converts the question into a vector.
3. The vector store searches for document chunks with similar vectors.
4. Relevant chunks are retrieved.
5. The retrieved text is added to the LLM prompt as context.
6. The LLM generates an answer based on that context.

This provides **semantic search**, rather than relying only on SQL or keyword matching.

---

## Document Ingestion

The knowledge base was prepared by:

1. Loading documents
2. Splitting them into chunks
3. Creating embeddings for each chunk
4. Storing them in **Chroma**

Example chunking configuration:

- ~1000 characters per chunk
- ~200 characters overlap
- `RecursiveCharacterTextSplitter`

Remember:

- **Embedding model** → creates vectors
- **Chroma** → stores and searches vectors

---

## LangChain for RAG

LangChain is useful for RAG because it provides abstractions that connect the different components with very little code.

However, **LangChain is not required for RAG**. The same pipeline can be implemented directly with only a little more code.

---

## Two Key LangChain Abstractions

### 1. LLM

Wraps a generative model such as OpenAI, Anthropic, or other providers.

Typical usage:

`llm.invoke(...)`

Its job is to **generate the final answer**.

### 2. Retriever

Wraps the retrieval layer, including the vector store and embedding setup.

Typical usage:

`retriever.invoke(query)`

Its job is to **return documents semantically similar to the user's question**.

---

## LangChain `invoke()`

Many LangChain components expose a common interface:

`component.invoke(input)`

Examples:

- `llm.invoke(...)`
- `retriever.invoke(...)`

Other execution methods also exist, such as streaming and asynchronous invocation.

Objects supporting this execution model are conceptually called **Runnables**, although this terminology is less prominent in LangChain 1.0.

---

## Complete RAG Architecture

**Question → Retriever → Relevant Documents → Prompt + Context → LLM → Answer**

The retriever handles **finding relevant knowledge**, while the LLM handles **generating the response**.

A RAG application can also return the retrieved source documents so users can see **where the answer came from**.

---

## Key Takeaway

LangChain simplifies RAG by connecting two main pieces:

- **Retriever** → finds semantically relevant document chunks
- **LLM** → answers the question using those chunks as context

The underlying RAG concept remains simple:

**Retrieve relevant information first, then generate the answer using that information.**
