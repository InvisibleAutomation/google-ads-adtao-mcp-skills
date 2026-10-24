---
name: adtao-strategy
description: Use when the user wants to plan which services a business should advertise on Google Ads, in what order and with what budget split - for a new account or when a quarter ends, figures change or a service is added. Prices each service from the market, asks the owner's figures once, proposes a launch order the client decides line by line, and hands each accepted service to adtao-launch. Requires the AdTAO MCP server.
---

# Service-line strategy

A strategy session ends in a campaign plan: one line per service, AdTAO's recommendation (launch or
hold) with its reason, budget, order and the figures behind it. The client decides each line, and the
client's decision wins: only the lines they accept, add or change are built. Nothing here changes Google
Ads; the plan and the decisions are AdTAO's records, shared with the account's team.

## Steps

1. **What is already known.** Read the client's notes with `get_notes` and the current plan with
   `get_campaign_plan`. Never ask again for a figure the notes already hold: use it, and name the note
   ("your note that you close 1 in 3 repair leads, Sam, 12 Oct"). The plan's standing constraints are
   services the client chose not to run, with the date and their reason; do not propose them again
   (step 5 says when they come back as a question).
2. **The services found.** `price_service_lines` with the website and the places served. Show one row
   per service: monthly searches, advertisers competing (with how many accounts the figure rests on),
   the cost-per-click range, conversion rate and cost per lead, and the season. A figure under its floor
   says so and names the wider group it was rolled up to. Then ask: "Is this list right? Anything you do
   that isn't on the site, or that you don't want to sell?"
3. **The owner's figures, in one message.** For each service kept, with blanks they can skip:
   - the average job value;
   - the margin or profit per job;
   - of 10 leads, how many become jobs (the close rate);
   - the jobs a month they can take;
   - which services they want more of;
   - whether one service leads to another (repair to replacement).
   Ask only what step 1 did not find. Save each answer with `add_note` as a fact with its service
   line, who said it and when (close rate as a share, 0.33 for 1 in 3). A close rate above 1 is refused:
   ask again.
4. **Re-rank with their figures.** `rank_service_lines` with the services and the figures saved in
   step 3. Show the three ranks (most competitive, least competitive, best value per pound) with the
   reason for each, and the formula's inputs for the value rank: leads per 100 of budget, close rate,
   profit per job. When an owner figure is missing, say which rank rests on the market alone.
5. **Propose a launch order and a budget split.** For each service: launch or hold, the reason in plain
   words ("£38 a lead, 1 in 3 close, £600 profit"), the monthly budget, the order, and what that budget
   buys against the jobs they can take. Save it with `create_campaign_plan`, passing every line with the
   figures its recommendation rests on and the owner's monthly budget; the lines' budgets fit inside it.
   The plan comes back with the lines its standing constraints changed:
   - a service the client skipped before is held, with their earlier reason; say so in one line;
   - once its review date has passed or its cost per lead or demand moved by more than 25%, it comes
     back as a question that cites the decision ("In October you chose not to run gutters because the
     margin was thin. Cost per lead has since fallen 40%; worth another look?"). Ask it as written.
6. **The client decides, line by line.** For each line, `record_client_decision` with the plan's id:
   accept, skip (with their reason, in their words, and the quote), add (a held or unlisted service,
   with a budget), or change (a new budget or order). Record who decided and, when they give one, the
   date to look again. Read back what will be built and what will not.
7. **Hand each accepted service to `adtao-launch`**, one campaign or ad group per service as the plan
   says, with the figures already saved: the launch interview then skips what is known.

Run the session again when a quarter ends, when the owner's figures change, or when a service is added.
The new plan replaces the old one; earlier decisions stay on record and still stand.

## Rules

- The client's decision wins. Never argue with a skip, and never re-recommend a skipped service except
  as the question the plan returns.
- A recommendation names its figures and where each came from; a figure resting on fewer than 30
  accounts or advertisers says "a small group" next to the count.
- Best value per pound needs the owner's figures. Before they answer, rank on leads per 100 of budget
  alone and say so; never fill in a close rate or a margin yourself.
- Outcomes are facts: "cost per lead £44 against the £38 the plan used". No "we told you so", no
  verdict on whether the client was right.
- No abbreviations and no tool names in what the user reads; money in the account's currency.

## Budget

About 8 calls: `get_notes`, `get_campaign_plan`, `price_service_lines`, one `add_note` per answer,
`rank_service_lines`, `create_campaign_plan`, one `record_client_decision` per line. Each step's calls
wait for the step before.

## Tools

`get_notes`, `add_note`, `get_campaign_plan`, `price_service_lines`, `rank_service_lines`,
`create_campaign_plan`, `record_client_decision`.
