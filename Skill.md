---
name: Planthood Recipes
description: Given a Planthood meal box confirmation email, find each meal on planthood.co.uk and compile the recipes into markdown.
---

# Planthood Meal Box Recipe Extractor

## Step 1 — Parse meal names

Extract only the meal/dish names from the user's email. Ignore everything else.

## Step 2 — Get the product catalogue

Fetch the product catalogue using curl (not Python requests):

```bash
curl -s "https://planthood.co.uk/products.json?limit=250&page=1" | python3 -c "
import sys, json
data = json.load(sys.stdin)
for p in data.get('products', []):
    print(p['handle'])
"
```

If 250 handles are returned, fetch page 2, etc.

## Step 3 — Match meals to slugs

Match each meal name from step 1 against the handles from step 2. Meal names won't be exact matches — match flexibly using the words in the handle.

## Step 4 — Fetch each recipe page

For each matched handle, fetch the product page and extract cooking instructions, ingredients, and nutritional info:

```bash
curl -s "https://planthood.co.uk/products/{handle}" | python3 -c "
import sys, re
html = sys.stdin.read()
text = re.sub(r'<[^>]+>', ' ', html)
text = re.sub(r'\s+', ' ', text)
idx = text.lower().find('cooking')
if idx > 0:
    print(text[max(0,idx-100):idx+3000])
"
```

If the output is truncated mid-step, re-fetch with an offset to get the remaining steps.

## Step 5 — Output

```markdown
# 🌱 Planthood Recipes — This Week's Box

## [Meal Name]
**Link:** https://planthood.co.uk/products/{handle}

### Cooking Instructions
[from the product page, or "Not available on product page" if missing]

### Ingredients
[from the product page]

---
```

Repeat for each meal. List any unmatched meals under "⚠️ Unmatched meals".
