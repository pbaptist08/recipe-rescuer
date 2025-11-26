# Recipe Rescuer V0 – Test Cases

## 1. Simple, plenty of ingredients
- Ingredients: eggs, spinach, onion, tomato, cheese, bread
- Diet: vegetarian
- Time: 20
- Tools: stovetop, oven
- Expectation: Some kind of toast/omelette/frittata; 20 min or less; only listed ingredients.

## 2. Very few ingredients
- Ingredients: pasta, garlic
- Diet: no preference
- Time: 15
- Tools: stovetop only
- Expectation: Simple garlic pasta OR a kind message that it's limited plus 2–3 shopping suggestions.

## 3. Vegan constraint
- Ingredients: chickpeas, spinach, onion, coconut milk, rice
- Diet: vegan
- Time: 30
- Tools: stovetop only
- Expectation: No dairy/animal products; maybe a curry or stew; 30 mins.

## 4. Allergy constraint (nuts)
- Ingredients: chicken, broccoli, soy sauce, peanut butter
- Diet: no nuts
- Time: 25
- Tools: stovetop only
- Expectation: Should avoid peanut butter or warn it's unsafe and suggest alternatives.

## 5. Unrealistic time
- Ingredients: dried beans, onion, tomato, spices
- Diet: vegetarian
- Time: 10
- Tools: stovetop only
- Expectation: Should say dried beans can't cook from scratch in 10 mins and suggest realistic alternatives.

## 6. Ambiguous input
- Ingredients: "leftover meat, some veggies"
- Diet: not sure
- Time: 30
- Tools: stovetop only
- Expectation: Ask a clarifying question instead of guessing.
