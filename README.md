# 🌱 Planthood Recipe Extractor

A Claude skill that takes a Planthood meal box confirmation email and compiles the cooking instructions into markdown.

## What it does

1. Parses meal names from your order confirmation email
2. Fetches the full product catalogue from Planthood's Shopify API
3. Matches your meals to product slugs
4. Fetches and parses each recipe page for cooking instructions, ingredients, and nutritional info
5. Outputs a clean markdown recipe card for the week's box

## Setup

1. Add `Skill.md` to a Claude.ai conversation or project knowledge
2. Ensure **code execution** is enabled (on by default in Claude.ai)
3. If you want Claude to read your confirmation email directly, link your **Google account** under Settings → Connected apps

Otherwise, just paste the email into the conversation.

## Usage

Start a conversation in the project and either:
- Paste your Planthood order confirmation email, or
- Ask Claude to check your Gmail for the latest Planthood email

### Example output

```
# 🌱 Planthood Recipes — This Week's Box

## Sichuanese Mapo Tofu
**Link:** https://planthood.co.uk/products/sichuanese-mapo-tofu

### Cooking Instructions
1. Heat oil in a wok...

### Ingredients
Tofu, chilli bean paste, Sichuan peppercorns...

---
```

Unmatched meals (if any) are listed at the end.

## How it works

Planthood runs on Shopify, which exposes a public `/products.json` endpoint. The skill uses `curl` (not Python `requests`, which is blocked in Claude's sandbox) to fetch the catalogue and extract product handles. It then matches meal names to handles, fetches each product page, and strips the HTML to extract recipe content.

## Project structure

```
├── Skill.md    # Claude skill definition
└── README.md
```
