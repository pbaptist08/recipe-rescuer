
# Prompt Engineering Notes – Recipe Rescuer (V0)

## 1. Context
**Project:** Recipe Rescuer  
**Goal:** Turn “ingredients you already have at home” into a realistic, quick recipe via an LLM, with a simple web UI.

**Tech stack (V0):**
- **Frontend:** Lovable (frontend + Supabase function)
- **Backend:** Supabase edge function → `supabase/functions/generate-recipe`
- **LLM:** `google/gemini-3-pro-preview` via Lovable’s AI gateway
- **Flow:** Frontend form → calls `generate-recipe` → returns recipe string → rendered in a recipe card

**PM Focus:**  
- How system prompts shape model behavior  
- How to design a structured user message from UI inputs  
- How to create test cases, observe failures, and iterate  

---

## 2. Inputs & Outputs

### 2.1 UI Inputs
The form collects:
- **Ingredients you have:** free text, comma-separated list
- **Diet preference:** No special diet, Vegetarian, Vegan, No pork, No nuts
- **Max cooking time (minutes)**
- **Available tools:** Stovetop, Oven, Microwave, Air fryer

These are passed to the backend and turned into a user message.

### 2.2 User Message (what the model sees)
Example structure:
```
Ingredients: eggs, spinach, onion, tomato, cheese, bread
Diet: Vegetarian
Max time: 20 minutes
Tools: Stovetop, Oven
Task:
Propose exactly 1 recipe that fits these constraints.
```
This “context engineering” step is important: instead of raw text, format the user input into a consistent template.

### 2.3 Desired Output Format
Plain text, structured recipe so the UI can render easily:
```
Recipe: <recipe name>
Estimated time: <X> minutes
Ingredients:
- <ingredient 1>
- <ingredient 2>
Steps:
1. <short step 1>
2. <short step 2>
Notes:
- <short note 1>
- <short note 2>
```
**Rules:**
- No markdown headings, no extra sections, no long essays.

---

## 3. System Prompt – Final Version (V0)
```
You are Recipe Rescuer, a home-cooking assistant.
Your job:
- Take the user's ingredients, diet, time limit, and tools.
- Suggest ONE realistic recipe a normal home cook could make with those ingredients.
- You may assume common pantry staples: salt, black pepper, cooking oil, and a splash of milk if needed.

INGREDIENT RULES:
- The main ingredients must come from the user's list.
- You may also use these pantry staples if needed: salt, black pepper, cooking oil, milk.
- Do NOT introduce completely new main ingredients (no new vegetables, meats, cheeses, etc.).

FORMATTING RULES (VERY IMPORTANT):
- Use ONLY plain text. Do NOT use Markdown headings (#, ##, ###), bold (**), backticks, or horizontal rules (---).
- Do NOT write an introduction like "Here's a delicious recipe". Start directly with the word "Recipe:".
- Your entire answer MUST follow exactly this layout:
Recipe: <recipe name>
Estimated time: <X> minutes
Ingredients:
- <ingredient 1>
- <ingredient 2>
Steps:
1. <short step 1>
2. <short step 2>
Notes:
- <short note 1>
- <short note 2>
- Do not add extra sections.
- Keep steps short and practical (3–6 steps).
- Keep notes short (1–3 notes).

EXAMPLE FORMAT (do NOT copy content, only structure):
Recipe: Quick Tomato Toast
Estimated time: 10 minutes
Ingredients:
- bread
- tomato
- salt
- black pepper
Steps:
1. Toast the bread until golden.
2. Slice the tomato and place on the toast.
3. Sprinkle with salt and black pepper.
Notes:
- Use ripe tomatoes for best flavor.
- You can drizzle a little oil if you have it.
```

**Key design choices:**
- Role & job – make it clear this is a home-cooking assistant, not a general chatbot.
- Ingredient rules – main ingredients must come from the user; only a small set of pantry staples are allowed.
- Very explicit formatting rules – specify no markdown + exact layout.
- One small example – gives the model a concrete pattern to imitate without locking it to specific content.

---

## 4. Test Case & Iterations

### 4.1 Core Test Case
**Input**
- Ingredients: eggs, spinach, onion, tomato, cheese, bread
- Diet: Vegetarian
- Max time: 20
- Tools: Stovetop, Oven

**Expectation**
- A realistic 20-minute recipe using those ingredients.
- Vegetarian.
- No major new ingredients.
- Short, structured output that UI can render nicely.

### 4.2 Early Behavior (before iterations)
Problems:
- Model added extra ingredients (milk, butter, olive oil, herbs).
- Ignored time constraints (~40–45 min).
- Used Markdown (headings, bold, horizontal rules).

Pain points:
- Hard to trust for “use what’s in your kitchen” scenarios.
- Hard to render consistently in UI.

### 4.3 Prompt Iterations
1. **Version 1 – basic spec**
   - Role, goals, some rules.
   - Still lots of markdown and extra ingredients.
2. **Version 2 – very strict rules**
   - “NEVER add any ingredient”, “NO markdown”, strong wording.
   - Reduced pantry staples to only salt/pepper/water.
   - Model still ignored some constraints and gave markdown.
3. **Version 3 – shorter + clearer + example (final)**
   - Simplified system prompt: fewer concepts, more direct rules.
   - Allowed small set of pantry staples (salt, pepper, oil, milk).
   - Added tiny example showing exact layout.
   - Switched to `google/gemini-3-pro-preview`.

**Result:** This combination was the turning point.

### 4.4 Final Behavior
Example output:
```
Recipe: Cheesy Tomato and Spinach Egg Skillet
Estimated time: 20 minutes
Ingredients:
- 2 eggs
- 1 cup spinach
- 1/2 onion
- 1 large tomato
- 1/4 cup cheese
- 2 slices bread
- Salt and black pepper
Steps:
1. Dice the onion and tomato into small pieces.
2. In a non-stick pan over medium heat, cook the onions and tomatoes until soft.
3. Stir in spinach and cook for 1 minute until wilted.
4. Crack eggs over mixture, season, cover with lid.
5. Simmer 5–7 minutes until whites set, sprinkle cheese to melt.
6. Toast bread and serve on the side.
Notes:
- Use tomato juices to prevent sticking.
- Add a tablespoon of water if pan gets too dry.
```

Matches goals:
- Uses only listed ingredients + pantry staples.
- Exact layout.
- Plain text, no markdown.
- Realistic ~20-minute recipe.

---

## 5. What I Learned (PM Perspective)
1. **Prompts are specs, not magic spells**
   - System prompts = lightweight behavior spec.
   - Must describe role, constraints, output format.
   - Models are only partially obedient.
2. **Prompt engineering = loop, not one-shot**
   - Define test cases → run → observe → adjust.
   - Multiple rounds needed for good balance.
3. **Shorter + concrete > long + vague**
   - Short prompt with critical rules + example + clear formatting worked better.
4. **Some constraints better handled in product/code**
   - Don’t rely only on prompts for strict safety/time/resource limits.
   - Validate inputs, post-process outputs, add guardrails.
5. **Model choice matters**
   - Different models respond differently.
   - Switching to `google/gemini-3-pro-preview` improved compliance.
