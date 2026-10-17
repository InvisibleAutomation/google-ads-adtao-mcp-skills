---
name: adtao-waste-hunter
description: Use when the user wants to find and stop wasted Google Ads spend in search terms and placements. Judges each term and site against evidence from many advertisers, not just this account, and stages the negatives or exclusions for the user's approval. Requires the AdTAO MCP server.
---

# Waste hunter

Waste is spend on searches or sites that do not convert for this account and, across many
advertisers, do not convert for anyone. The account's own history alone cannot tell a slow term
from a dead one; AdTAO's cross-account evidence can.

## Steps

1. **Context.** Read `account-context.yaml` if present (break-even cost per lead, brand rules). Say
   whether it was found; without it, say brand protection uses the account's own brand terms only.
2. **Search terms.** Call `get_evidence_led_negatives` for the account. Each term's own 90-day
   wasted spend and its cross-account verdict are in the themed groups and the top-by-waste table;
   the proposed batch lists only the terms. Use each term's own figures; a term with no figure of
   its own is shown as "not broken out", never given a share of a total. For any term the user
   questions, `get_fleet_term_evidence` gives the detail.
3. **Held negatives.** Call `get_negatives_watch_status`: terms held for a verdict earlier. Say each
   one's status in plain words: "now ready to exclude" (enough new clicks, still no conversions),
   "still being watched", or "started converting, keep it". Report the ready-to-exclude and the
   converting ones first.
4. **Placements.** Call `get_account_placement_risk`. It reads where the ads served in the last 30
   days (display, video, Performance Max), flags websites that behave badly across other
   advertisers, and states which content and brand exclusions are in place. When it includes a
   `risk_matrix` with an exclusion list, use that list as the starting point.
5. **Rank** by money at stake. Keep the user's brand terms and anything converting for this account
   off the list, whatever the cross-account evidence says.
6. **Present** two tables, search terms and sites, each with the same columns: term or site ·
   spend here (each row its own figure) · the evidence (advertisers and conversions elsewhere, or
   "this account only") · verdict (exclude, watch, keep) · why. Use the tool's own verdict and its
   reason; for a term excluded on this account's data alone, the why is the tool's ("exact match, so
   only that exact search is blocked"). Call a term waste across accounts only when the evidence
   column shows other advertisers. A term whose evidence you do not show is not in the table. Totals you state must add up from the rows you show; if
   the tool gives a larger total, say the rest is in terms not listed.
7. **Predict, if asked.** `predict_change` with `add_negative_keyword` gives what tended to happen
   after negatives were added elsewhere; name the ledger and show `all_changes`.
8. **Stage** what the user picks: one `stage_manager_action` with `add_negative_keyword` and
   `{negatives: [{term, match_type}], shared_list_name}`. Then follow house rules §6 for the code.

## Rules

- A term converting for this account is never excluded on fleet evidence.
- Every other term keeps the tool's own verdict. Do not change "add" to "watch" (or the reverse) on your
  own judgement, and never say the house rules require a change they do not state.
- A total you give is the tool's total for the terms you show; your own sums and monthly figures are
  labelled "my estimate from the 90-day figure".
- Phrase or exact match for negatives unless the user asks for broad.
- Report spend in the account's currency, with the window (e.g. last 30 days).

## Budget

About 6 calls for one account: steps 2 to 4, one optional `predict_change`, one stage call, one
approve call.

## Tools

`get_evidence_led_negatives`, `get_fleet_term_evidence`, `get_negatives_watch_status`,
`get_account_placement_risk`, `predict_change`, `stage_manager_action`, `approve_and_execute`.
