# recipe-rescuer

LLM-powered Recipe Rescuer that suggests realistic recipes from your ingredients, built to learn and showcase **prompt engineering (V0)** and **RAG (V1)** with a simple web UI.

---

## Live demo

➡️ Try it here: https://pb-recipe-rescuer.lovable.app

---

## Versions

### V1 – Prompting + RAG (current)

> Status: **In progress / Planned**

**Everything in V0, plus:**

- ✅ Connects to a small recipe knowledge base (curated examples / tips).
- ✅ Uses embeddings + retrieval to ground the LLM with real recipe patterns.
- ✅ Prompts include both:
  - user inputs (ingredients, diet, time, tools), and  
  - retrieved snippets from the knowledge base.
- ✅ Additional notes in `docs/rag-notes-v1.md` (design, trade-offs, test cases).

**Why this version exists**

- ✅ To learn and demonstrate:
  - How RAG changes behavior vs prompt-only.
  - How to structure documents and chunking.
  - How to evaluate whether RAG is actually helping (or just adding complexity).

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

- ✅ Designing the **system prompt**:
  - Role, goals, constraints, output format.
- ✅ Iterating on prompt behavior using test cases:
  - Extra ingredients, markdown formatting, time realism.
- ✅ Capturing learnings in docs:
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
