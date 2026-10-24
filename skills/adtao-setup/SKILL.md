---
name: adtao-setup
description: Use the first time AdTAO works on an account, or when its goals change. Saves to the client's AdTAO notes the business facts every other AdTAO skill needs (margins, break-even cost per lead or ROAS, goals, area types, brand rules, budget owner, who approves changes). Requires the AdTAO MCP server.
---

# AdTAO setup

The skills judge an account against its own economics. This skill collects them once and keeps them
in the client's notes on AdTAO (`get_notes`, `add_note`), which every AI app and the team see; a
`account-context.yaml` (schema: `schemas/account-context.schema.json`) can mirror them in a working folder.

## Steps

1. **Account.** Confirm the account (`list_accessible_accounts` if not named).
2. **Where setup stands.** Call `get_notes` and `setup_status` for the account; do not ask again for a
   fact the notes already hold (say which note it came from). If AdTAO does not work on it yet, say
   so and ask; on a yes, `choose_accounts` (`get_account_activation` lists the other accounts under
   the same manager). If its industry is a guess or missing, ask what the business does, then
   `get_account_industry` with their words and `set_account_industry` once they confirm; do this
   before any comparison with other accounts.
3. **What AdTAO already knows.** Call `get_account_intelligence` and
   `get_conversion_action_inventory` for the account. Pre-fill what they show (currency, the
   conversion actions that count, budgets) and say where each figure came from. If the current
   Google Ads bidding target (target cost per action or ROAS) is shown, compare it with the user's
   goal; if it is not, say only that it was not read (do not infer "none set"). Copy the account's
   name exactly as AdTAO shows it; if no tool showed a name, write that it was not read.
4. **Ask, in one message, only what is still missing** (always ask; never mark something as declined
   unless the user said so):
   - what a lead or sale is worth (or the margin), so break-even cost per lead or ROAS follows,
   - the goal: a target cost per lead, a target ROAS, or a monthly volume at a budget,
   - the monthly budget and who owns it,
   - areas served and whether they are city, suburban or rural (keep the user's own word),
   - brand rules (words never to use, competitors never to name) — always ask; an empty list is only
     written when the user says there are none,
   - who approves changes, and the email addresses of the agency's people and the client's people
     (ask for them; leave the list empty only if the user declines).
5. **Echo it back** as the YAML you are about to write and ask for a yes. Do not save on silence.
6. **Save the goal in AdTAO** (the set_account_goal tool) (metric and value, the account or one
   campaign), so AdTAO's checks, pacing and reports judge the account against it. This changes
   nothing in Google Ads.
7. **Save each fact** with `add_note` (kind fact, with key and value: job_value, profit_per_job or
   margin, close_rate, break_even_cost_per_lead, target_cost_per_lead or target_roas, monthly_budget,
   budget_owner, approver, areas_served, areas_not_served, brand_words_never, competitors_never_named),
   source owner or account_manager and who said it; brand rules and other answers in their words as
   kind preference or constraint. A new value replaces the old one, which stays on record. Where there
   is a working folder, also write `account-context.yaml` as a mirror (one per account:
   `account-context.<customer_id>.yaml` when several accounts share a folder).
8. **Hand back** to the request that triggered setup, if any.

## Rules

- Never invent a margin or target. Leave a field empty and say it is unknown.
- The goal the user states is the goal. If it differs from the bidding target in Google Ads, say so
  plainly and offer to stage the target change through the house-rules approval path; do not change
  it here.
- The file stays on the user's machine. Do not paste it anywhere else.

## Budget

4 calls: account list if needed, account intelligence, conversion inventory, set_account_goal.

## Tools

`list_accessible_accounts`, `get_account_intelligence`, `get_conversion_action_inventory`,
`set_account_goal`.
