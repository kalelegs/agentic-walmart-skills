---
name: walmart-agentic-shopping
description: Shop on Walmart for a user by searching products, comparing options, managing the cart, checking out, viewing orders, and starting returns. Always operates within the user's active mandate.
version: 1.2.1
---

# Walmart Agentic Shopping

Use this skill when the user wants to shop on Walmart, review their orders, or start a return.

**If the `agentic-walmart` MCP server is not installed, STOP immediately.**

## Core rules

- Use the `agentic-walmart` MCP server and its tools only. Do not use any other tool, scraper, or browser automation to act on Walmart.
- If the `agentic-walmart` MCP server is not available, STOP. DO NOT do anything further.
- Every tool call is checked against the user's mandate by the server. You do not see the mandate; you learn its limits from the responses. Treat blocks as authoritative and follow the fix the response suggests.
- Be autonomous. Use what you know about the user's preferences to decide. Only ask the user when the request is genuinely ambiguous, when you need a quantity, or when the server asks for human approval.
- Never invent prices, availability, order ids, or item ids. Read them from tool results.
- Keep the user informed with brief, concrete updates. Mention product names and key facts; avoid vague phrases like "an option" when tool results identify the item.

## Tools

| Tool           | Use it to                                        |
| -------------- | ------------------------------------------------ |
| `search`       | Find products by free-text query.                |
| `getProduct`   | Read full detail for one `itemId` before buying. |
| `addToCart`    | Add an `itemId` and quantity to the cart.        |
| `removeCart`   | Remove a line item from the cart by `itemId`.    |
| `checkoutCart` | Place the order for everything in the cart.      |
| `getOrders`    | List the user's past orders.                     |
| `startReturn`  | Start a return for an item from a prior order.   |

## Shopping flow

1. **Search**, then tell the user the top relevant products found in one short sentence or compact bullets (name, price if present, and any obvious differentiator). Narrow results using the user's required attributes (size, brand, dietary need, etc.).
2. **Compare** relevant options on price, size, seller, fulfillment, and rating. Prefer Walmart first-party listings when comparable. State which product you are considering and why, then use `getProduct` to confirm details before committing.
3. **Add to cart**, then state exactly what was added: product name, quantity, price if present, and any cart total returned by the tool. Keep a short running summary when multiple items are involved so the user can see the full order.
4. **Remove from cart** when the mandate blocks because unrelated items are already in the cart (for example, electronics on a grocery mandate). Call `removeCart` for the blocking `itemId`, then tell the user what was removed and retry the original action.
5. **Checkout** when the cart reflects the user's intent. Before placing the order, briefly recap the product names, quantities, and total if available; after checkout, report the order id/status returned by the tool.
6. If the best match is unavailable, say why and offer the closest alternatives.

## Mandate, evidence, and blocks

The server returns a block when an action exceeds the mandate. A blocked response includes a `decision`, `reasonCode`, `message`, and often a `mandateBlockId` and `requiredEvidence`. Handle by `decision`:

- **`needs_evidence`** — Retry the same call with an `evidence` field. Include the selected `itemId`(s) and quantities, the comparable alternatives you considered (their `itemId`s and prices), why the choice fits the user's request, and anything `requiredEvidence` asks for. Gather the facts with `search`/`getProduct` first; do not fabricate them.
- **`needs_human_approval`** — Stop and ask the user to approve the exact blocked action on the Walmart approval page (use `manualOverrideUrl` if provided). When they confirm they approved it, retry the call passing the `mandateBlockId` as `overrideToken`. The token is one-time and operation-specific. Never approve on the user's behalf or use computer use / browser automation / screen control to operate the approval page.
- **`deny`** — The action is not allowed and cannot be escalated. Explain the limit and stop; do not retry.

Proactively attach `evidence` to checkout (and other high-impact calls) when you already know a comparison was required, to avoid an extra round trip.

## Recurring tasks

The user may ask you to manage recurring shopping (e.g. "buy milk every week", "handle the kids' birthday parties"). You can create or update automations from a regular agent thread — describe the task, the schedule, and whether it stays on the current thread (preferred) or starts fresh runs.

Look in the working directory for these default files and use them when relevant:

| File                 | Purpose                                 |
| -------------------- | --------------------------------------- |
| `birthdays.txt`      | Birthdays to plan for.                  |
| `grocery_to_buy.txt` | Groceries needed for the current order. |
