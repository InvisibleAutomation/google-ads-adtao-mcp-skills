---
name: adtao-competitors
description: Use when the user asks who their Google Ads competitors are, what those competitors say on Google, what changed among them, or which angles to test in their own ads. Reads AdTAO's live searches of the account's own keywords. Requires the AdTAO MCP server.
---

# Competitors

AdTAO searches a sample of the account's own keywords in its own locations every week and keeps what
it finds. Most of what it finds is ordinary search results and map listings; paid ads are a small
share. Each competitor carries `seen_as` (ad, search result, map listing): say which, and never call a
competitor an advertiser when it was only seen as a search result or map listing.

## Steps

1. **Freshness.** Call `get_competitor_landscape`. If it says no collection exists, or the newest
   collection is more than 14 days old (`newest_collection_days_old`; say it as "collected N days
   ago"), call `refresh_competitor_data` once (it collects at most once a
   day per account) and tell the user the new collection lands later; answer from what is stored.
2. **Who competes.** From the landscape: each competitor's share of the searched keywords, its usual
   position, how persistent it is (every, most, or some collections) and `seen_as`. Give the
   `result_mix_note` in one sentence.
3. **What changed.** Call `get_competitor_changes` when the user asks what is new, or the landscape's
   change summary shows anything. Keep three groups apart: new to the account or gone from it, moved
   onto more or fewer keywords, and text or price changes. A competitor missing from one keyword has
   not left.
4. **Angles.** The landscape's `angles` are recurring themes in competitors' wording, with an example
   and a test for the user's own ads. Copy the idea, never the words. Use the tool's level: persistent
   (a competitor kept it two weeks or more), common (three or more competitors), seen (one or two,
   weak); never "proven".
5. **Detail, if asked.** `get_competitor_elements` and `get_competitor_element_patterns` give the
   stored headlines and descriptions; `get_competitor_transparency_ads` gives ads from Google's Ads
   Transparency Center when any were gathered for the account.
6. **Act, if asked.** Offer a new ad with the angle the user picks: `stage_manager_action` with
   `create_rsa` (created paused), then house rules §6 for the code.

## Rules

- Competitors are named by website, or by business name for a map listing that shows no website.
- Say how old the newest collection is, in days ("collected 5 days ago"), not as a date.
- A competitor seen only as a search result or map listing "shows" or "lists" wording; only one seen
  as an ad "runs" an ad. Name whose ads the paid results were when there are any.
- An angle's label must match its examples; rename it from the example if it does not.
- The user's own site never appears as a competitor; if it does, say the collection mislabelled it.
- Google's auction insights are not available through the Google Ads API for most accounts. Say that
  (most accounts, not "this account") when the user asks for impression share against named competitors.

## Budget

About 3 calls: landscape, changes, and one optional refresh or detail call. Staging adds two.

## Tools

`get_competitor_landscape`, `get_competitor_changes`, `refresh_competitor_data`,
`get_competitor_elements`, `get_competitor_element_patterns`, `get_competitor_transparency_ads`,
`stage_manager_action`, `approve_and_execute`.
