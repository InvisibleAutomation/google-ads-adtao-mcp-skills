---
name: adtao-tracking-truth
description: Use when the user asks whether their Google Ads conversion numbers are real, why conversions look wrong, or how to set tracking up. Checks the conversion actions, what counts toward bidding, duplicate and dead actions, and stages any missing conversion action with its tag. Requires the AdTAO MCP server.
---

# Tracking truth

Every other recommendation rests on the conversion numbers. If they are wrong, bidding and budget
advice makes things worse. Check them first.

## Steps

1. **Inventory.** Call `get_conversion_action_inventory`. It lists the conversion actions the
   advertiser controls, which count toward bidding (primary), how they count (one or every), their
   values and their last-30-day volume, plus a counting diagnosis.
2. **Account view.** Call `get_account_intelligence` and read its measurement health: whether
   conversions are being recorded at all, and how far AdTAO can trust them.
3. **Is the tag on the site?** Call `check_conversion_tags`. It reads up to three of the pages the
   live ads send people to and reports the Google Ads tag ids found and whether they are this
   account's. When it only sees Tag Manager, say the Google Ads tag may load through it and that
   step 1's volumes are the real test. Recorded conversions show that some tracking fires; they do
   not prove the website tag fires when the volume comes from call uploads or GA4.
4. **Run every check below and report each one** (found / not found), worst first:
   - nothing counts toward bidding, or the action that counts records nothing,
   - a page view, click or other light action counts as a conversion,
   - two actions record the same thing (both primary, similar volumes),
   - an action counts every conversion where one per click is right (leads),
   - actions with no volume in 30 days that still count,
   - the landing pages carry another account's Google Ads tag, or no tag and no Tag Manager.
   Then order what you found by money at stake (the biggest problem first), not by this list.
5. **Explain** each in one plain sentence with the action's name and the numbers that show it.
6. **Fix.** For each problem found, give the fix and where it is made. For a missing action, stage `create_conversion_action` (name, category, counting type,
   value). After approval, the result carries the tag to put on the site; give it to the user with
   where it goes. For a setting change on an existing action, say exactly where to change it in
   Google Ads: those are not staged changes yet.
7. **Re-check later.** Always end by telling the user when to run this check again (after a few
   days of conversions on any new or changed action).

## Rules

- Google's automatic local actions (directions, website visits) are not the advertiser's to change
  and never count toward reported conversions; leave them out.
- Do not recommend bid or budget changes while a tracking problem stands. Say tracking comes first.
- GA4 and Tag Manager are not read by AdTAO yet; do not claim anything about them.

## Budget

About 5 calls: inventory, account view, tag check, one stage, one approve.

## Tools

`get_conversion_action_inventory`, `get_account_intelligence`, `check_conversion_tags`,
`stage_manager_action`, `approve_and_execute`.
