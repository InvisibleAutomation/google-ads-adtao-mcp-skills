---
name: adtao
description: Start here for any Google Ads question on AdTAO. Routes the request to the right AdTAO skill, loads the house rules, and on first use shows what AdTAO can see that a plain Google Ads connection cannot. Requires the AdTAO MCP server.
---

# AdTAO (router)

Read `HOUSE_RULES.md` before anything else in this session.

## First use: what AdTAO adds

If this is the first AdTAO request in the conversation, call `adtao_capabilities` once and give the
user a short card (figures on it come from AdTAO's own capability summary; say so) (five lines at most) of what AdTAO adds beyond their own account data:
search-term and placement evidence across many advertisers, settled outcomes of past changes,
market comparison for accounts like theirs, and changes applied only after they approve them in
AdTAO. Then answer what they asked.

## Route

| The user wants | Use |
|---|---|
| Save goals, margins, who approves (first time, or when goals change) | `adtao-setup` |
| Find wasted spend: search terms, placements, held negatives | `adtao-waste-hunter` |
| Know whether the conversion numbers are real; set up tracking | `adtao-tracking-truth` |
| Build a new Search campaign | `adtao-launch` |
| Who the competitors are, what changed among them, angles to test | `adtao-competitors` |
| The monthly client report or commentary | `adtao-client-report` |
| What Google's automation did: AI Max, Performance Max placements, Google-made assets | `adtao-pmax-guard` |
| Turn someone else's Google Ads skill into an AdTAO one | `adtao-upgrade-skill` |
| Write a new AdTAO skill from a one-line job | `adtao-author-skill` |
| "Is this normal for an account like mine?" | Both: `get_market_comparison` (its issues against similar accounts) and `get_performance_vs_benchmark` (its cost per lead and rates against peers). Give each figure with how many accounts it rests on |
| "What would happen if I changed X?" | `predict_change` (name the ledger; show `all_changes`) |
| "What's wrong with this account?" | `archetype_diagnosis` (stored), or `archetype_detect` with `mode: "async"` for a fresh scan |
| A named AdTAO tool | Call it directly, inside the house rules |

If an `account-context.yaml` exists in the working folder, read it first and use its goals and
roles. If it does not, and the request depends on goals (targets, budgets, "is this good"), offer
`adtao-setup` before answering.

## "Is this normal for an account like mine?"

Always two calls, both: `get_market_comparison` (its open issues against similar accounts) and
`get_performance_vs_benchmark` (its cost per lead and rates against peers). Report each figure with
the number of accounts behind it, and say "a small group" when that number is under 30. Describe
what the account does or does not have; do not grade the account beyond what the two returned.

## Find the account

If the user did not name one, call `list_accessible_accounts` and ask which. Never guess.

## Budget

One `adtao_capabilities` call on first use; otherwise the budget of the skill routed to.

## Tools

`adtao_capabilities`, `list_accessible_accounts`, `get_market_comparison`,
`get_performance_vs_benchmark`, `predict_change`, `archetype_diagnosis`, `archetype_detect`, and any
tool of the skill being routed to.
