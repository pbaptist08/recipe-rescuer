# recipe-rescuer
LLM-powered Recipe Rescuer that suggests realistic recipes from your ingredients, built to learn and showcase prompt engineering and RAG with a simple web UI

# Recipe Rescuer (V0)

Recipe Rescuer is a tiny LLM app that suggests realistic recipes based on:
- Ingredients you already have
- Diet preferences
- Time limit
- Available tools (e.g. stovetop only)

## Why I built this

I’m using this project to learn and showcase:
- **Prompt design** (role, rules, output format, edge cases)
- How to **wire an LLM into a real UI** (Lovable + OpenAI)
- Later: **RAG**, Supabase, and simple evaluation/logging

## V0 Scope

V0 is intentionally small:
- Single page UI
- One LLM call per request
- No database, no RAG yet

See the `prompts/` folder for:
- My system prompt
- Test cases
- Iteration notes
