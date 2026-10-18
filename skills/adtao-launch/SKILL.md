---
name: adtao-launch
description: Use when the user wants a new Google Ads Search campaign built for an account. Builds it from evidence (keywords that convert across many advertisers, negatives that waste across them, competitors' angles) following AdTAO's house playbook, and stages the whole campaign for approval, created paused. Requires the AdTAO MCP server.
---

# Launch a Search campaign

**Stop rule, before anything else:** if the account has no primary conversion action, or the tag
those actions rely on is not seen on the landing page (the tag check's site tracking says no), do not
build, even when the user asks for the build.
Say what is missing, why a campaign that cannot count its leads cannot be judged, and offer to check
the tracking first. That is the whole answer.

The whole campaign goes to Google as one change: budget, campaign, locations, ad groups, keywords,
ads, negatives, sitelinks, callouts and snippets all land, or none of it does. It is created paused;
turning it on is a separate change the user approves.

## Steps

1. **Context.** Read `account-context.yaml` if present: break-even cost per lead, goal, locations,
   brand rules. Without a break-even figure, ask for one: the playbook sets the budget from it.
2. **Tracking first.** `get_conversion_action_inventory`, then `check_conversion_tags` on the landing
   page. The playbook needs a primary conversion action and the tag seen on the site: the check's
   site tracking reads the page and its Tag Manager containers, and counts a Google Analytics 4 tag
   for actions imported from Google Analytics. Set the build's `tracking.tag_seen` from it. When it
   says no, or cannot tell, stop here: say what is missing and offer to check the tracking first. Do not assemble
   or show a build until both are in place; a campaign that cannot count its leads cannot be judged.
3. **Where and at what cost.** `get_account_geo_regime` gives the area type of each location
   (major metro to rural) and the cost-per-lead band for that area type from comparable
   accounts. Use the band for the budget floor and the target; when the locations mix city and
   rural, build two campaigns. When the read says the cross-account bands are unavailable, say
   so, use the vertical benchmark instead, and name that as the fallback.
4. **Keywords.** `get_keyword_gaps` for themes the account does not cover yet. Check the candidates
   with `get_fleet_term_evidence` (up to 25 per call) and keep the ones that convert across other
   advertisers: conversion rate at or above the vertical's median and 3 or more advertisers
   behind it. Group the kept terms by theme into 2 to 10 ad groups, each holding 4 to 20 keyword
   entries (each term in exact and phrase is two entries, so at least 2 terms a group); a theme
   with one term joins its nearest theme rather than standing alone. A keyword the account's own negatives block is dropped or its blocker is staged for
   removal in the same batch. Then `get_keyword_volume` on the kept terms for the build's
   locations (Google Keyword Planner): drop a theme with no searches there, say
   which themes are seasonal (peak and low month), and say how many clicks a day the daily
   budget buys at the low and the high top-of-page bid. These are Google's estimates; say so.
   Finish this step (evidence and volume) before calling anything in step 5.
5. **Negatives.** Inherit first, then add, as the playbook's negatives rule says: `get_hierarchy_inventory`
   lists the lists the account can attach (from last night's group sweep; say so); the manager
   tree's universal and industry lists go in the build's `negative_lists` with their tier, so they
   are attached in the same request as the campaign,
   competitors excluded by default on the full name as a phrase and never on a single token
   that is a place name or a word in the build's keywords; then the account's own terms from
   `get_evidence_led_negatives`. Every tier is attached or declined with a reason.
6. **Ad angles.** `get_competitor_landscape` gives competitors' angles with an evidence level, and
   `get_competitor_element_patterns` the wording patterns behind them. Write the ads from angles
   marked common or persistent; copy the idea, never the words. When no collection exists, call
   `refresh_competitor_data` once, say the angles land later, and write the ads from the
   theme's own terms with an override that says so.
7. **Assemble the build** in the playbook's format: `playbook_version`, `campaign_type`,
   `break_even_cpl`, `campaign` (name, daily_budget, bidding_strategy, locations), `ad_groups`
   (name, keywords, ads), `negatives`, `extensions` (sitelink, callout, structured_snippet),
   `tracking`. Where the build departs from the playbook on purpose, list it under `overrides`
   with a reason.
8. **Show the user** the build, citing the playbook version, short enough to read in one go:
   one table row per ad group (its keywords, conversion rate elsewhere, advertisers, monthly
   searches); for the ads, each ad group's three strongest headlines and the count of the rest; one
   line per negative list and the new terms in a single list; the extensions as one list each;
   the budget and bidding with the band it rests on; and every override. The full build goes to
   the staging call, not into the text, unless the user asks to see it.
9. **Stage** it: one `stage_campaign_build` with the account and the build; the lists in
   `negative_lists` are attached with it, all or nothing. A build that departs from the playbook without a reason is
   refused with the rule it broke; fix it or add the override. Then house rules §6 for the code.
10. **Born clean.** After it is created, run `archetype_detect` on the new campaign and read its
    AI Max state with `get_ai_max_status`: the playbook expects no findings and AI Max off
    unless an override says otherwise. Hold the new campaign to the build-time findings (structure,
    settings, targeting); a paused campaign with no history trips the performance checks, which are
    read after it has run. Report what the scan found. Then offer `enable_campaign`
    as a separate change, and an image for the ads with `add_image_asset` (field type AD_IMAGE)
    if the user has one.

## Rules

- The campaign is always created paused.
- Run the steps in order: tracking, then keywords, then negatives, then angles.
- What the user reads uses plain words: "target cost per lead", "conversion rate", never tool or skill
  names, never words joined with underscores. Your own arithmetic (a monthly figure from a daily
  budget) is labelled as your estimate.
- Every keyword in the build has its evidence shown, or is marked "this account only".
- A figure resting on fewer than 30 accounts or advertisers says "a small group" next to the count.
- A build cites the current playbook version. If staging says the playbook is not active yet, say
  that builds open once it is, and offer to keep the build ready.

## Budget

About 14 calls: inventory, tag check, geo regime, gaps, one or two term-evidence calls, keyword
volume, list inventory, negatives, landscape and element patterns, one build stage call, one
approve call, and the two born-clean reads.

## Tools

`get_conversion_action_inventory`, `check_conversion_tags`, `get_account_geo_regime`,
`get_keyword_gaps`, `get_fleet_term_evidence`, `get_hierarchy_inventory`,
`get_evidence_led_negatives`, `get_competitor_landscape`, `get_competitor_element_patterns`,
`refresh_competitor_data`, `get_keyword_volume`, `stage_campaign_build`, `stage_manager_action`,
`approve_and_execute`, `archetype_detect`, `get_ai_max_status`.
