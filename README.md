# recipe-rescuer

LLM-powered Recipe Rescuer that suggests realistic recipes from your ingredients, built to learn and showcase **prompt engineering (V0)** and **RAG (V1)** with a simple web UI.

---

## Live demo

➡️ Try it here: https://pb-recipe-rescuer.lovable.app

---

## Versions

### V2 – RAG with Supabase + embeddings + PDFs/docs

> Status: **Planned**

Version 2 upgrades the RAG setup to be closer to a real-world system by ingesting content from docs/PDFs into the same Supabase-backed knowledge base.

**Everything in V1, plus:**

- ✅ Extend the existing `recipe_kb` table:
  - Add rows representing chunks from one or more docs/PDFs (e.g., a “house cookbook” or safety document).
  - Keep using the same `embedding` (pgvector) column for all rows (patterns, rules, and doc chunks).
- ✅ Ingestion pipeline:
  - Extract text from one or more docs/PDFs,
  - Chunk the text into smaller pieces,
  - Generate embeddings for each chunk,
  - Insert them into `recipe_kb` with metadata (e.g., `source_type = 'pdf'`, `source_name = 'cookbook.pdf'`).
- ✅ Query-time vector search (same pattern as V1):
  - Build a query string from user inputs,
  - Embed the query,
  - Use Supabase/pgvector to retrieve the most relevant rows (patterns + doc chunks),
  - Build the `Context:` block from those rows.

**UI changes (building on V1):**

- ✅ “Why this recipe?” now also highlights **sources**, e.g.:
  - Used patterns:
    - Quick Egg Skillet  
  - Used sources:
    - cookbook.pdf – “Chapter 2: Eggs & Veggies”
- ✅ Badge can be updated to:
  - `✨ Powered by RAG (V2: Supabase + PDFs)`

**What I learn in V2**

- 🎯 Real-world-ish RAG architecture:
  - KB table + embeddings + vector search + prompt construction.
- 🎯 How docs/PDFs become “rows of text” that the model can be grounded on.
- 🎯 How schema design (tags, time ranges, tools) affects retrieval quality.
- 🎯 How to explain end-to-end RAG in an interview:
  - Ingestion → chunking → embeddings → retrieval → prompt → answer → UX explanation.

---

### V1 – RAG with Supabase + embeddings (curated KB only)

> Status: **Planned / In progress**

Version 1 adds RAG on top of the prompt-only V0, using **Supabase + embeddings** over a curated knowledge base (no PDFs yet).

**Everything in V0, plus:**

- ✅ A small, hand-curated **knowledge base** stored in Supabase:
  - Recipe patterns (e.g., “Quick Egg Skillet”, “Toast + Topping”),
  - Safety notes (e.g., simple egg safety),
  - Diet/substitution rules (e.g., vegetarian guidance).
- ✅ A `recipe_kb` table in Supabase with columns like:
  - `id`
  - `pattern_type` (e.g., 'pattern', 'safety', 'diet_rule')
  - `pattern_name`
  - `text` (the actual pattern/rule snippet)
  - `diet_tags`, `ingredient_tags`, `tools`
  - `min_time_minutes`, `max_time_minutes`
  - `embedding` (pgvector vector for semantic search)
- ✅ Vector-based retrieval in the backend:
  - Build a query string from user inputs (ingredients, diet, time, tools),
  - Generate an embedding for the query,
  - Use Supabase/pgvector to retrieve the most relevant KB rows,
  - Build a `Context:` block from those rows and inject it into the LLM prompt.
- ✅ Richer response from the backend:
  - Returns the recipe,
  - Plus metadata like which patterns/rules were applied.

**UI changes (lightweight):**

- ✅ “Why this recipe?” section under the recipe card, e.g.:
  - Based on your ingredients and time, this recipe uses:
    - Quick Egg Skillet  
    - Egg Safety – Cook Thoroughly
- ✅ Small badge on the recipe card:
  - `✨ Powered by RAG (V1: Supabase + embeddings)`

**What I learn in V1**

- 🎯 Practical RAG mental model with a real Supabase KB:
  - Retrieve → add to prompt → answer.
- 🎯 How curated patterns/safety rules make outputs more consistent & explainable.
- 🎯 How to design a `Context:` section and control how the model uses it.
- 🎯 How to wire Lovable → Supabase → embeddings → LLM in a clean, simple way.

---

### V0 – Prompting only (shipped)

> Status: **Live**

**What it does**

- ✅ Simple web form collects:
  - Ingredients you have  
  - Diet preference  
  - Max cooking time  
  - Available tools
- ✅ Sends inputs to a Supabase edge function.
- ✅ Calls an LLM with:
  - A carefully designed **system prompt** (Recipe Rescuer).
  - A **structured user message** summarizing the form inputs.
- ✅ Returns a single recipe in a consistent, plain-text format:
  - `Recipe`, `Estimated time`, `Ingredients`, `Steps`, `Notes`.

**What I focused on in V0**

- 🎯 Designing the **system prompt**:
  - Role, goals, constraints, output format.
- 🎯 Iterating on prompt behavior using test cases:
  - Extra ingredients, markdown formatting, time realism.
- 🎯 Capturing learnings in docs:
  - See `docs/prompt-engineering-notes-v0.md`.
---

### V0 – Prompting only (shipped)

> Status: **Live**

**What it does**

- ✅ Simple web form collects:
  - Ingredients you have  
  - Diet preference  
  - Max cooking time  
  - Available tools
- ✅ Sends inputs to a Supabase edge function.
- ✅ Calls `google/gemini-3-pro-preview` with:
  - A carefully designed **system prompt** (Recipe Rescuer).
  - A **structured user message** summarizing the form inputs.
- ✅ Returns a single recipe in a consistent, plain-text format:
  - `Recipe`, `Estimated time`, `Ingredients`, `Steps`, `Notes`.

**What I focused on in V0**

- 🎯 Designing the **system prompt**:
  - Role, goals, constraints, output format.
- 🎯 Iterating on prompt behavior using test cases:
  - Extra ingredients, markdown formatting, time realism.
- 🎯 Capturing learnings in docs:
  - See `docs/prompt-engineering-notes-v0.md`.

---

## Tech stack

- **Frontend:** Lovable (React + Tailwind, auto-generated UI)
- **Backend:** Supabase edge function (`supabase/functions/generate-recipe`)
- **LLM:** `google/gemini-3-pro-preview` via Lovable’s AI gateway
- **Docs:** `docs/` folder with prompt + RAG notes

---

## Roadmap

- [ ] V1 – Add RAG:
  - [ ] Small internal recipe knowledge base (few curated examples).
  - [ ] Embeddings + retrieval layer.
  - [ ] Document RAG design + trade-offs.
- [ ] V2 – Data & analytics:
  - [ ] Store user queries + responses (Supabase).
  - [ ] Basic analytics on recipe usage / failure cases.
