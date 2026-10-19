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

The same holds when the Business Profile is not linked (step 7): give the linking steps word for
word, say what has been read so far, and stop. Do not assemble or show a build until the link shows.

The whole campaign goes to Google as one change: budget, campaign, locations, ad groups, keywords,
ads, negatives, sitelinks, callouts and snippets all land, or none of it does. It is created paused;
turning it on is a separate change the user approves.

## Steps

1. **Context, and what to ask a new account.** Read `account-context.yaml` if present. `adtao_capabilities`
   gives the current campaign playbook: its version, status and the rules of each campaign type; the
   build cites that version. Then ask what is missing, in one message, before any build:
   - the **website**, and the page for each service if they differ;
   - the **places served** (cities, ZIP codes or counties) and any the business does not serve;
   - the **services**: residential or commercial, emergency or by appointment, brands and equipment
     they work on and the ones they will not, the smallest job they take;
   - the **monthly budget** the owner will spend. Until they confirm it, the build waits;
   - the **break-even cost per lead**. If they do not know it, ask what a job is worth and the
     margin, multiply them, show the figure, and use it only once they agree;
   - their **goal** cost per lead, if they have one.
2. **Tracking first.** `get_conversion_action_inventory`, then `check_conversion_tags`. A new account has
   no ads to take a page from: pass the website (and the service pages) as `urls`. The check reads each
   page and its Tag Manager containers, and counts a Google Analytics 4 tag for actions imported from
   Google Analytics. Set the build's `tracking.tag_seen` from its site tracking. When there is no
   primary conversion action, or site tracking says no or cannot tell, stop here: say what is missing
   and offer to set the tracking up first. Calls and form submissions are both leads. The same read's
   lead tracking says whether enhanced conversions for leads and auto-tagging are on; when either is
   off, say that booked jobs cannot be matched to clicks later, and stage `enable_auto_tagging` with
   the build. Ask whether the website's form keeps the click id (gclid) with the lead.
3. **Industry.** `get_account_industry` with the business's own words. Show the industry it has (or,
   for a new account, the closest candidates with how many accounts share each and how many keyword
   categories each loads) and ask which fits. When the person confirms one that is not already set,
   `set_account_industry` with `confirmed_by_person` true. It changes AdTAO's records only. An
   industry with no keyword categories cannot build themes; pick another.
4. **Where, at what cost, and whether the goal is real.** `find_locations` turns each place served
   into Google's id. When a name is ambiguous, ask which place is meant; never guess an id. The
   build's `campaign.locations` takes the ids and `campaign.location_names` the names, and staging
   refuses a build whose ids are not the places it names. `get_account_geo_regime` gives the area
   type and the cost-per-lead band for accounts that already run campaigns; for a new account it is
   unavailable, so say so and use the industry's figures as the fallback. Then hold the goal up to
   the figures before sizing anything:
   - goal above break-even: say the business loses money on every lead at that target, and ask
     whether it is a wish or the number they will pay. Do not size from a wish;
   - goal below the area band or below the top-of-page bid (step 5): say the market costs more, show
     the band and the bids, and size from the band; keep their number as the aim, labelled as not
     yet seen on accounts like theirs;
   - no goal: use 70% of break-even, and say it is a stand-in, not their target.
5. **Keywords.** `get_keyword_gaps` for themes the account does not cover yet; on a new account every
   theme of its industry is uncovered. Check the candidates with `get_fleet_term_evidence` (up to 25
   per call) and keep the ones that convert across other advertisers: conversion rate at or above
   the vertical's median (the evidence read gives the median and marks each term against it) and 3
   or more advertisers behind it. Add "near me" and "emergency" as themes of their own for home
   services when their terms pass the same test. A city written into a kept term ("air duct
   cleaning spring tx") has no evidence of its own: mark it "this account only" and never show the
   plain term's figures as its own. The playbook allows at most 2 such terms in an ad group, so add
   only the cities where Keyword Planner shows searches. Group the kept terms by theme into
   2 to 10 ad groups, each holding 4 to 20 keyword entries (each term in exact and phrase is two
   entries, so at least 2 terms a group); a theme with one term joins its nearest theme. A keyword
   the account's own negatives block is dropped or its blocker is staged for removal in the same
   batch. Then `get_keyword_volume` on the kept terms for the build's location ids (Google Keyword
   Planner): drop a theme or city with no searches there, say which themes are seasonal, and say
   how many clicks a day the daily budget buys at the low and the high top-of-page bid. These are
   Google's estimates; say so. Finish this step before calling anything in step 6.
6. **Negatives.** Inherit first, then add: `get_hierarchy_inventory` lists the lists the account can
   attach (from last night's group sweep; say so); the manager's universal and industry lists go in
   the build's `negative_lists` with their tier, attached in the same request as the campaign. At
   most 20 lists attach to one campaign: keep the account's own and the industry lists first, then
   the universal ones, and decline the rest in `negative_tiers_declined` with that reason. A new
   account under no swept manager has none: decline the tier with that reason. Competitors are
   excluded on the full name as a phrase, never on a single word that is a place name or a word in
   the build's keywords. Then the account's own terms from `get_evidence_led_negatives` (none on a
   new account), and the services the business said it does not do. Every tier is attached or
   declined with a reason.
7. **Ad angles and the business's own facts.** `get_competitor_landscape` gives competitors' angles
   with an evidence level, and `get_competitor_element_patterns` the wording behind them. When no
   collection exists, call `refresh_competitor_data` once with the build's keywords and the place
   names (a new account has no keywords of its own), say the angles land later, and write from the
   theme's terms with an override that says so. `get_site_facts` with each ad group's page as `url`
   gives what the business itself says: headings, phone, links to its own pages, and its existing
   ads and assets. Each ad group's final URL is the page for its service. Every claim (prices, speed,
   sizes, guarantees) must appear in those facts or come from the user; sitelinks point only to
   links on the page or existing sitelinks; the call extension uses a number found there. Anything
   else is listed for the user to confirm and kept out of the build. Never put a phone number in ad
   or extension text (Google refuses it). The same read says whether the Business Profile is linked.
   When it is not, stop (see the stop rule at the top): give the person its linking steps, word for
   word, and assemble nothing until the link shows; the build does not stand in for it. Its stored profile is an earlier read that
   can be old: use a claim from it only after the person confirms it, and never its phone number.
8. **Is each page ready?** `check_landing_page` on each ad group's page, with its service. A stop
   (the page errors, or asks for nothing: no form, no button, no call to action) means that ad group
   is not built until the page is fixed; say what to fix. A hold (no trust signals, slow on a phone,
   a heading that does not name the service) is said in one line and is the person's to accept as an
   override. When speed was not measured, say so. Carry the check's budget sentence into step 9.
9. **The budget the owner signs.** The playbook's floor is 3 times the target cost per lead a day
   (the checker holds at least 2.1 times break-even), and it is a floor for the account, not for
   each campaign: two campaigns share it. One campaign per service line (for example residential
   and commercial) only when the budget carries 3 times the target for each line; otherwise the
   lines are blocks of ad groups in one campaign. A brand campaign (`campaign_type` brand_defence)
   is built in the same launch, ahead of the service campaign, so the business's own name is covered
   against directories and lead sellers; its budget is part of the same figure. The service
   campaign's `other_campaigns_daily_spend` is the brand campaign's daily budget plus what the
   account's running campaigns spend a day. When the named budget cannot carry both floors (the
   brand campaign's is half of break-even a day), show both figures and ask which to build first;
   never reserve an amount for a brand campaign that is not in a build. Show one figure before
   anything is staged: the monthly budget the owner named, this campaign's daily budget times 30.4, and what that leaves. If the
   campaign does not fit inside the named budget, do not stage: say what to cut (fewer themes or
   places, or one campaign instead of two). When the named budget buys few leads at the market's
   bids, say so and keep the budget they named. Say the rate in the same sentence: "sized for the
   industry's conversion rate, from N accounts; this page has not been shown to reach it", or,
   after a hold, size the first month from clicks at the Keyword Planner bid.
10. **Assemble the build** in the playbook's format: `playbook_version`, `campaign_type`,
   `break_even_cpl`, `campaign` (name, daily_budget, bidding_strategy, locations, location_names,
   `conversion_goals`), `ad_groups` (name, keywords, two ads each with the group's own final URL;
   the playbook needs at least two), `negatives`, `negative_lists`, `extensions` (sitelink, callout, structured_snippet, call),
   `tracking`. `conversion_goals` bids on the leads (calls and form submissions) and not on soft
   events (directions, page views, form starts, chat opens), nor on purchases for a business that
   does not sell online. In the service campaign,
   `campaign.brand_terms` carries the business's own name, so the brand campaign takes those searches,
   and `campaign.ip_exclusions` the public IP addresses of the office and the owner's home (ask for
   them), so the first conversions come from customers. Leave `campaign.brand_list_shared_set_id`
   out unless the person names a Google brand list; a negative keyword list is not one. A place
   named in an ad is one of the places served. Where the build departs from the
   playbook on purpose, list it under `overrides` with a reason, naming the rule exactly as the
   playbook does (for example `budget.min_daily_vs_break_even_cpl`).
11. **Show the user** the build, citing the playbook version, short enough to read in one go: one
    table row per ad group (keywords, conversion rate elsewhere, advertisers, monthly searches, its
    page); for the ads, each ad group's three strongest headlines and the count of the rest; one line
    per negative list and the new terms in a single list; the extensions as one list each; the places
    by name; the budget figure from step 9; and every override. The full build goes to the staging
    call, not into the text, unless the user asks to see it.
12. **Stage** it: one `stage_campaign_build` with the account and the build, and with it, for a new
    account, `pause_auto_applied_recommendations` and `enable_auto_tagging` through
    `stage_manager_action`, so Google's own changes do not rewrite the campaign after approval. A
    build that departs from the playbook without a reason is refused with the rule it broke; fix it
    or add the override. Then house rules §6 for the code.
13. **Born clean.** After it is created, run `archetype_detect` on the new campaign and read its AI Max
    state with `get_ai_max_status`: the playbook expects no findings and AI Max off unless an override
    says otherwise. Hold the new campaign to the build-time findings (structure, settings,
    targeting); a paused campaign with no history trips the performance checks, which are read after
    it has run. Report what the scan found. Before offering `enable_campaign`, ask the person to place
    one test call and send one test form, and to see both arrive in Google Ads and in the inbox they
    read. Then offer `enable_campaign` as a separate change; its approval page shows what the campaign
    will spend a day and a month, with the account's other running campaigns. Then an image
    for the ads with `add_image_asset` (field type AD_IMAGE) if the user has one.

## Rules

- The campaign is always created paused.
- Run the steps in order: tracking, industry, places, keywords, negatives, angles, budget. Make one step's
  calls at a time, never a later step's call alongside an earlier one.
- Clicks a day from a budget and a bid is your own arithmetic: work it out (daily budget divided
  by the bid) and label it as your estimate, not Google's.
- No abbreviations in what the user reads: "conversion rate" not "CVR", "accounts" not "accts",
  "Google Analytics" not "GA4".
- What the user reads uses plain words: "target cost per lead", "conversion rate", never tool or skill
  names, never words joined with underscores. Your own arithmetic (a monthly figure from a daily
  budget) is labelled as your estimate.
- Every keyword in the build has its evidence shown, or is marked "this account only".
- A figure resting on fewer than 30 accounts or advertisers says "a small group" next to the count.
- A build cites the current playbook version. If staging says the playbook is not active yet, say
  that builds open once it is, and offer to keep the build ready.

## Budget

About 20 calls: capabilities, inventory, tag check, industry (and its confirmation), locations, geo
regime, gaps, one or two term-evidence calls, keyword volume, list inventory, negatives, landscape,
element patterns, site facts, one build stage call, one approve call, and the two born-clean reads.

## Tools

`get_conversion_action_inventory`, `check_conversion_tags`, `get_account_geo_regime`,
`get_keyword_gaps`, `get_fleet_term_evidence`, `get_hierarchy_inventory`,
`get_evidence_led_negatives`, `get_competitor_landscape`, `get_competitor_element_patterns`,
`refresh_competitor_data`, `get_keyword_volume`, `find_locations`, `get_account_industry`,
`set_account_industry`, `check_landing_page`, `get_site_facts`, `adtao_capabilities`,
`stage_campaign_build`, `stage_manager_action`,
`approve_and_execute`, `archetype_detect`, `get_ai_max_status`.
