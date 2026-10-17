---
name: adtao-upgrade-skill
description: Use when the user has a Google Ads skill written for another tool or a plain Google Ads connection and wants an AdTAO version. Keeps the skill's job and output, replaces its data gathering with AdTAO tools, adds AdTAO's checks, and says which AdTAO skill already covers the job. Requires the AdTAO MCP server.
---

# Upgrade a skill to AdTAO

## Steps

1. **Read** the skill the user gives (a SKILL.md or pasted text) and show the user, one line each:
   its job, its inputs, its output, the data it gathers and how.
2. **Already covered?** If an AdTAO skill does this job (`adtao-waste-hunter`,
   `adtao-tracking-truth`, `adtao-setup`), say so first. The upgraded skill then becomes a thin
   wrapper: its steps say "follow adtao-waste-hunter steps 1 to 6", then add only what the original
   did differently (its output format, its thresholds as a filter). Do not copy the AdTAO skill's
   steps into it.
3. **Replace data gathering.** Map each raw query to the AdTAO tool that answers it with evidence:
   - search terms → `get_evidence_led_negatives` (adds cross-account evidence),
   - placements → `get_account_placement_risk`,
   - conversions → `get_conversion_action_inventory`,
   - "is this good" → `get_market_comparison` / `get_performance_vs_benchmark`,
   - "what if" → `predict_change`,
   - anything with no specialised tool → `gaql_search`, last.
4. **Add AdTAO's checks** the original lacks, written as rules in the new skill: tracking first (refuse bid advice on broken
   tracking), never exclude what converts for this account, the approval path from house rules §6
   for any change.
5. **Add a step to the new skill** (not just to your reply) that ends its output with one block:
   "You didn't ask, but…" with the single most important thing AdTAO found that the original
   would have missed, or nothing if none.
6. **Write** the new SKILL.md: frontmatter (`name`, `description` that starts "Use when"), the
   steps, a **Rules** section, a **Budget** line and a **Tools** list, in the house-rules format.
7. **Explain** to the user, in five lines at most, what changed and why.

## Rules

- Describe AdTAO tools only by the one-line purposes in step 3 and the house rules; no windows,
  defaults, categories, columns or behaviours that are not written there. A claim about how the new
  skill will do better than the original is [TEST] until a run shows it.
- Credit the original author in an HTML comment on the first line of the new file.
- Keep the original author's job and output shape; credit them in a comment at the top.
- Do not add tools the job does not need.
- If the job fails the Claude test (a plain Google Ads connection does it as well), say so and
  keep the upgrade minimal.

## Tools

None of its own: it writes a file. To check a mapping, it may call the tools in step 3 on an
account the user names.
