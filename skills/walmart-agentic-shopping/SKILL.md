---
name: walmart-agentic-shopping
description: Shop on Walmart for a user by searching products, comparing options, managing the cart, and preparing checkout with explicit user confirmation before purchase.
version: 1.0.0
---

# Walmart Agentic Shopping

Use this skill when a user wants an agent to shop on Walmart.com on their behalf.

## Responsibilities

- Search Walmart for requested products.
- Compare relevant options by price, size, seller, fulfillment method, and ratings.
- Ask follow-up questions when the request is ambiguous.
- Add user-approved items to the Walmart cart.
- Review the cart and prepare checkout details for user confirmation.

## Required Inputs

Collect any missing details before acting:

- product name or shopping list
- quantity
- budget or price ceiling, if any
- required brand, size, color, model, or dietary constraints
- shipping, delivery, or pickup preference
- whether substitutes are acceptable

## Operating Rules

1. Prefer Walmart first-party listings when comparable options exist.
2. Preserve the exact item URL, title, quantity, seller, and price for anything you recommend or add to cart.
3. If the best match is unavailable, explain why and offer the closest alternatives.
4. If search results are broad, narrow them using the user's required attributes before selecting an item.
5. If multiple items are requested, keep a running cart summary so the user can confirm the full order.

## Safety Boundaries

- Do not place an order, submit payment, or finalize checkout without explicit user confirmation of the exact cart contents and totals.
- Do not guess missing personal information such as address, payment details, or substitution preferences.
- If Walmart requires sign-in, hand off clearly to the user for authentication and then continue once access is available.
- Flag third-party marketplace sellers when they are not clearly sold by Walmart.

## Recommended Workflow

1. Confirm the shopping goal and missing constraints.
2. Search Walmart for each requested item.
3. Compare the best matching options.
4. Present a concise recommendation when a choice is needed.
5. Add only user-approved items to the cart.
6. Re-open the cart and verify titles, quantities, seller, subtotal, fees, and estimated delivery or pickup details.
7. Ask for explicit approval before proceeding to any irreversible checkout step.

## Output Format

When reporting back, include:

- selected items and quantities
- Walmart product links
- item prices and estimated totals
- fulfillment method
- any unresolved choices or risks before checkout
