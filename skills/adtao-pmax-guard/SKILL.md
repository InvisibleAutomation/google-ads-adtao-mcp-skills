---
name: adtao-pmax-guard
description: Use when the user asks what Google's automation is doing in their account: AI Max for Search, Performance Max placements, or ad text and assets Google made itself. Shows what Google changed or created, what it cost, and stages the switches or exclusions the user picks. Requires the AdTAO MCP server.
---

# PMax guard

Google's automation changes where ads show and what they say. This skill shows what it did, what it
cost, and gives the user the switches back.

## Steps

1. **AI Max.** Call `get_ai_max_status`: which Search campaigns have AI Max on, which Google moved to it
   (with the date and the defaults it applied), and the exclusions in place. For campaigns Google moved
   across the user's accounts, `get_ai_max_migrations` lists them with spend before and after.
2. **Where it sent traffic.** For a campaign with AI Max on, `get_ai_max_expansions` gives the landing
   pages beyond the ad's own and the headlines AI Max wrote, with cost and conversions. Report pages
   marked broken or blocked first.
3. **Placements.** `get_account_placement_risk` reads where display, video and Performance Max ads
   served in the last 30 days and flags websites that behave badly across other advertisers.
4. **Google-made assets.** `get_account_health` lists the assets Google made and is serving
   (`google_made_assets`), each with its link name and text.
5. **Present** one table per area (AI Max, placements, Google-made assets), each with exactly these
   columns: what Google did · spend and conversions since · evidence · what you can do. Use each
   tool's own figures and verdicts; "not given" where a tool gave no figure.
6. **Stage what the user picks**, each through `stage_manager_action` then house rules §6:
   - AI Max settings: `set_ai_max_campaign_settings` or `stage_ai_max_setting`; a landing page to
     keep AI Max off: `add_ai_max_url_exclusion`.
   - Before switching AI Max off for good, offer `stage_ai_max_experiment` to measure it first.
   - Google-made text: `set_campaign_asset_automation` to stop new ones; `set_google_made_asset_status`
     with the link names to pause ones already serving.
   - Websites and apps: `exclude_placement` with `website` or `app_id` (account level by default, which
     Performance Max honours), one per site or app, from the placement risk read; a content type the
     account should never show on: `exclude_placement` with `content_label`.
   - A new asset group, when the user wants one: `create_asset_group` with its text, the ids of images
     already in the account (add one first with `add_image_asset`) and optional search themes. It is
     created paused; `enable_asset_group` turns it on as a separate change.

## Rules

- AI Max is not judged on one day of expansions. Say which days the figures cover.
- One count per thing: if two reads give different counts (campaigns, Performance Max campaigns),
  say which read each comes from and why they differ, or use one.
- Every table has the spend and conversions column; write "not given" where the tool gave none.
- Anything about Google that no tool returned is general knowledge: mark it [TEST], or leave it out.
- Plain words: "set up for approval", "Performance Max", "Google moved it to AI Max".
- Pause Google-made assets; never remove them (removal cannot be undone by the user).
- An experiment's readout (`get_ai_max_experiment_readout`) is the only place to say AI Max caused a
  result.

## Budget

About 5 reads for one account (status, expansions, placements, health, optional migrations), then one
stage call per change and one approve call for the batch.

## Tools

`get_ai_max_status`, `get_ai_max_migrations`, `get_ai_max_expansions`, `get_ai_max_experiment_readout`,
`get_account_placement_risk`, `get_account_health`, `stage_ai_max_setting`, `stage_ai_max_experiment`,
`stage_manager_action`, `approve_and_execute`.
