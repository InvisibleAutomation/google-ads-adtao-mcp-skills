---
name: adtao-client-report
description: Use when the user wants a client update for a Google Ads account - the monthly report, a weekly update or a daily digest. Drafts it from AdTAO's existing reads - results against the goal, spend against budget, the trend and last year, campaigns, similar accounts, what was done, issues and what is next - with marked places for the account manager's own notes. Nothing is sent. Requires the AdTAO MCP server.
---

# Client report

A draft an agency can send to its client after light edits. It answers the client's questions first -
are we on goal, are we on budget, what did you do, what is next - and keeps every AdTAO trust rule:
each figure has its source and window, a figure that could not be read says so, a change and a result
are "after", not "because", and nothing is sent.

## Steps

1. **Account notes.** If an `account-context.yaml` is in the working folder or was given to you, read it:
   `goal` (with `in_users_words`), `budget.monthly`, `people` (whose email addresses are the agency's and
   whose the client's), and `reporting`: `result_label` (leads, sales, calls, bookings), `strategy`,
   `planned_updates`, `audience`. In the draft they are "your account notes", never the file name. If
   none were given, the first line of your answer (before the draft) says: "No account notes were
   provided; tell me this quarter's plan and next month's, or edit the marked places." In the draft
   itself, never mention notes that do not exist ("no goal was given in your account notes").
2. **Period.** A monthly report unless the user asks for a weekly update or a daily digest (Sir Rob,
   22 Oct 2026: one skill, three rhythms). The period is the one the user names, else the last complete
   month, Monday-to-Sunday week, or day. Everything below is written for the month; for a week or a day,
   swap in this table and keep every rule:

   | | Week | Day |
   |---|---|---|
   | Compared with | the week before, and the same week last year | the same weekday a week before |
   | Read 2 | `gaql_search` `SELECT segments.week, metrics.cost_micros, metrics.conversions, metrics.conversions_value, metrics.clicks, metrics.impressions FROM customer WHERE segments.date BETWEEN` the Monday 53 weeks back and the week's Sunday (read 4 is then not needed) | `gaql_search` `SELECT segments.date, …` the same fields `FROM customer WHERE segments.date BETWEEN` 7 days before the day and the day |
   | Trend | weeks running | none |
   | Budget | the month's budget × 7 ÷ days in the month; the line reads "Spent {spend} of {budget} ({share}%), this week's share of the budget." with no working shown | `metrics_budget_pacing` with `date_range` THIS_MONTH_TO_DATE; the line reads "So far this month: {spend} of {budget} ({share}%), {on track \| ahead \| behind}." and nothing else |
   | Compared with similar accounts | as for the month | left out |
   | Changes (read 7) | the week | the day |
   | Title | `{Account name}: week of {Monday's date}` | `{Account name}: {date}` |
   | Last section | "Next week" | none |

   A weekly or daily row counts only when it covers every day of the period. Use the account's own
   currency (`customer.currency_code` from read 3) for every money figure, with its symbol.
3. **Read** (9 calls, 2 more only when needed; on the free plan wait any `retry_after` you are given):
   1. `get_account_intelligence`: the goals saved in AdTAO (`platform_state.goals`: metric, target, and
      whether it is for the account or one campaign) and the budgets saved in AdTAO.
   2. `get_monthly_performance_comparison` with `account_ids` [the account] and `months_back` 13: the
      month, the month before, the same month last year and the trend.
   3. `gaql_search` for the month, campaign by campaign: `customer.descriptive_name, customer.currency_code, campaign.name,
      campaign.status, campaign_budget.amount_micros, metrics.cost_micros, metrics.conversions,
      metrics.conversions_value, metrics.clicks, metrics.impressions, metrics.search_impression_share`
      `FROM campaign WHERE segments.date BETWEEN` the month's first and last day `AND metrics.cost_micros > 0`.
   4. Only for a revenue or return-on-ad-spend goal, or when step 3 shows conversion value: `gaql_search`
      `SELECT segments.month, metrics.cost_micros, metrics.conversions_value FROM customer WHERE
      segments.date BETWEEN` the first day 13 months back and the month's last day.
   5. `metrics_budget_pacing` with `date_range` CUSTOM and the month's `start_date` and `end_date`, only
      when step 1 shows a budget saved in AdTAO.
   6. `get_performance_vs_benchmark` (window `LAST_30_DAYS`).
   7. `get_changelog` three times, each with the month's `start_date` and `end_date` and
      `skip_manager_list_copies` true: (a) `entity_type` CAMPAIGN_BUDGET, `limit` 50: the budget changes,
      with `budget_changes_not_in_change_history`; (b) `entity_type` CAMPAIGN, `limit` 50: bidding, status
      and other campaign settings; (c) no `entity_type`, `limit` 1: only its `total_count`. Busy accounts
      record tens of thousands of targeting and keyword changes a month, so they are counted, not listed.
   8. `archetype_diagnosis`: the open issues.
   9. `get_experiment_readout` only when read 7 or the notes mention an experiment in the month.
   Add `get_market_comparison` only if the user asks how the account's issues compare with similar
   accounts, and `get_change_provenance_benchmark` only for the internal version (step 7).
4. **Choose the figures by the goal** (the goal saved in AdTAO; else the notes' `goal.kind`). The
   account goal always comes first (Sir Rob, 22 Oct 2026): it sets the headline figures and the summary's
   verdict. Campaign goals come after it, as secondary lines. Only when no account goal is saved does the
   first campaign goal lead.

   | Goal | Headline figures | Supporting |
   |---|---|---|
   | cost per conversion (cost per lead) | results, cost per result | spend against budget, conversion rate |
   | conversions | results, spend against budget | cost per result, impression share |
   | conversion value or return on ad spend | revenue, return on ad spend | spend against budget, results |
   | clicks or click-through rate | clicks, click-through rate | results, cost per result |
   | none saved | spend, results, cost per result | and one line: no goal is saved |

   Call results by `result_label` (leads, sales, calls, bookings) everywhere, else "conversions".
   Conversion rate is results ÷ clicks; return on ad spend is conversion value ÷ cost, written as money
   ("£33.36 of sales for every £1"), never as "33.36x"; revenue is conversion value. A campaign goal is
   measured on that campaign's own row from read 3, naming the campaign, never on the account's figures;
   if the campaign had no spend that month, say the goal could not be measured this month.
5. **Write the draft** by filling in this template. Before it, nothing but the no-notes line (step 1)
   when no notes were given. Keep its headings and line shapes; write nothing
   the template does not ask for: no extra paragraphs, notes, marked places or "before you send" lists.
   After it come only the house rules' three closing lines (Next step, Based on, Not known). They agree
   with the draft: no issue, figure or source the draft does not show, no check that was not run, no
   reason of your own. Next step is exactly: "Edit the marked places, then send it yourself." Not known
   lists only figures a read returned as unavailable and what the notes lacked, in one short line
   each: never how a tool returned its results, where a goal came from, a figure that is blank because
   its count is zero, or general Google Ads knowledge. Sources in plain words ("your account's monthly figures", "AdTAO's latest check
   of the account"). `{…}` is filled from the reads; `[…]` lines stay
   for the user exactly as written. Results ("leads") stand for `result_label`.

   ```
   # {Account name}: {Month Year}
   _Draft for review. Edit before sending; nothing has been sent._

   ## Summary
   - {On goal | Off goal | No goal is saved}: {headline figure} against a target of {target}.
   - Budget: {spend} of {budget} ({share}%), {under budget | on budget | over budget}.
   - Biggest move: {the headline or supporting figure with the largest change} {better | worse} by {n}% on {last month}.
   > [Your note: why this moved]

   ## Against your goal
   {Goal in the client's words, if the notes have them.} Target {target}; this month {figure}: {on | off} target.
   Campaign goals: {campaign}: target {target}; this month {figure}: {on | off} target.   (one line each, after the account goal)

   ## Spend and budget
   Spent {spend} of {budget} ({share}%). Budget: {your agreed budget | the budget saved in AdTAO |
   from your campaigns' daily budgets as set now}.{ Budgets changed during the month.}

   ## This month, last month and last year
   | | {Month} | {Last month} | vs last month | {Month last year} |
   |---|---|---|---|---|
   | {each headline and supporting figure} | … | … | {better / worse / flat, n%} | … or "no full figures" |
   Trend: {figure} has {risen | fallen} {n} months running.   (or: {figure} {rose | fell} this month, after {fell | rose} the month before.)
   > [Your note: anything behind the trend]

   ## By campaign
   | Campaign | Spend | {results} | {cost per result | return on ad spend} |
   | {five biggest by spend} | … | … | … |
   | Other campaigns ({n}) | {sum of the remaining rows} | … | … |

   ## Compared with similar accounts
   - {Metric} {this account's figure}: {say_this, word for word} (last 30 days).
   - {the by-campaign-type sentence, word for word} (this account: {its figure}; last 30 days).

   ## What we did and why
   {strategy from the notes, or: > [Your note: this quarter's plan]}
   - {Date}: {action} {on what}{, from {old} to {new}}.
   - {Date range}: budget on {campaign} from {old} to {new}; not in the change history, so who made it is not recorded.
   - Experiment {name}, {dates}: {Google's arm results as given}.

   ## Issues and concerns
   - {the issue's own description, as one plain sentence} {its suggested action}.
   Red flags: {each flag as a sentence with its figure, or: none this month}.

   ## Next month
   - {each planned update}   (or: > [Your plan for next month], then the top two fixes from the issues)
   ```

   How to fill it:
   - **Goal.** On or off target uses the goal's figure; a campaign goal uses that campaign's row. With
     no goal, the section is only the line that no goal is saved.
   - **Budget.** The first of: the notes' `budget.monthly`; a budget saved in AdTAO covering the month
     (read 5); else the campaigns' daily budgets from read 3 × days in the month. Under 100% is under
     budget, 100–105% on budget, over 105% over budget. For a month not yet over, add "my estimate from
     spend to date: {month-end figure}".
   - **Months.** A month counts only when `days_covered` equals `days_in_month` and it has spend; else
     "no full figures". The trend run counts back from the report month while each month moved the same
     way; it stops at the first month that went the other way.
   - **Campaigns.** Figures only: no remark on any row. Campaign names are the account's own names,
     written exactly as they are, square brackets and all (the one place an underscore or a short form
     may appear). The table's totals are the rows' own; never set them against the month's total or
     explain a difference.
   - **Similar accounts.** One line per metric the benchmark scores, with this account's own figure from
     the benchmark (`value`, or the by-campaign-type figures for conversion and click-through rate). The
     sentence is `say_this` (or the by-campaign-type sentence, with SEARCH written "Search",
     PERFORMANCE_MAX "Performance Max" and ALL "all") and nothing else: no `verdict` or `headline`
     wording, no explanation of the account counts, no note of your own. Leave a metric out
     under 10 accounts; under 30 add "(a small group)".
   - **Changes.** From read 7 (a) and (b) only. Each line is date, action, the campaign it was on, and old
     and new values where the change list has them, oldest first; a change without values is its action
     alone. Never remark on the change list itself (no "not shown", "values not recorded", "type not
     given"). No reason, no purpose, no link to a result. At most 12 lines: the rest of (a) and (b) become
     "and {n} more budget and campaign changes", n being their `total_count`s less the lines shown. Then
     budget changes no change history records, one line each (at most 6, then "and {n} more"). Last line:
     "And {N} changes to keywords, targeting, ads and assets." where N is (c)'s `total_count` less (a)'s
     and (b)'s; leave the line out when N is 0. Nothing from (c) is listed, only counted. With no changes
     at all, the section is the strategy line alone. Nothing else in this section.
   - **Issues.** Only issues `archetype_diagnosis` lists in full (with a description); the ones it gives
     only as counts are left out and not mentioned. At most five, by priority, and only those the latest
     check still saw (leave out any it marks as not seen in the latest check). Each is the issue's description and, when the tool gives
     one, its suggested action, in plain words, nothing added: no dates, no effects, no counts of checks.
     With none, the line is "No open issues in AdTAO's latest check of the account." A tracking red flag is only an issue about measuring conversions.
   - **Counts** everywhere, red flags and experiments included, follow the rounding rule below ("about
     30 leads"). Return on ad spend is in money form in every column ("£33.36 for every £1").
   - **Red flags** are only the five kinds below, never a tool's caution (a caution such as "under 30
     clicks" may be quoted beside its figure, not as a flag), and come only from the sections above (a tracking red flag repeats an issue listed above,
     with its count when the issue gives one): off goal; results down more than 15% on last month; cost per result worse by more
     than 15%; spend over 105% or under 85% of the budget; each open issue about measuring conversions.
6. **Direction in words.** Every change says better, worse or flat for this goal, not only a signed
   percentage: cost and cost per result are better when lower; results, revenue, return on ad spend,
   conversion rate, click-through rate and impression share are better when higher; within ±2% is flat.
   Spend is "up" or "down" and judged only against the budget, never better or worse.
7. **Internal version.** Only when the user asks for it: an appendix after the draft with who changed
   the account (people, Google's own suggestions, other tools, AdTAO) and how changes like these went
   across accounts, from `get_change_provenance_benchmark`, each lane's figure only when the tool gives
   one, with its accounts.
8. **Edit with the user.** Keep their wording where they change it; keep every number as the tool gave
   it, with its window.

## Rules

- One source per figure: the month's totals (spend, results, clicks) come from read 2 and the campaign
  rows from read 3; never show two versions of the same total.
- No reasons of your own, for results or for changes: no general Google Ads knowledge, no "this is
  normal", no "this can limit serving", no why a change was made unless the notes say. A change is its
  action, date and values. A campaign row is its figures, with no observation added. A tool's own
  caution may be quoted.
- Counts as whole numbers: "3 leads", not "3.0". Google can count part of a result, so a figure that is
  not whole is rounded and written "about 21 leads".
- A change is better, worse or flat by its percentage: work the percentage out; flat only when it is
  under 2% either way.
- The summary's lines use only what the sections found: its trend is the counted run.
- Not in the draft: how a tool decides (such as an on-track band), capitals from tool results ("ON
  TRACK"), and these words: tool, read, separate read, live read, cohort, diagnosis, scan, priority
  score, highest-priority checks, entries (say "items", "keywords", "audiences"), result label, record,
  window of data, payload.
- A tracking issue is one about measuring conversions (conversion actions, tags, goals, counting);
  anything else is not a tracking issue.
- No link between a change and an issue or a result unless a tool made it; never "sits alongside".
- No people in the client draft: no names or email addresses, and not who made which change (that is
  for the internal version).
- Never fill a section with a guess. A figure a tool did not return is "not available this month", with
  the tool's reason. The marked places stay marked until the user writes them; never write a reason why
  a number moved.
- "After", not "because", for any change and the result that followed it; an experiment's measured arm
  result is the one thing quoted as a result of a change.
- Every figure about other accounts carries how many accounts it rests on.
- The month's figures run through the last complete day; say so for a month not yet over.
- Keep each figure from the section that gave it; do not add up shortened lists (use the totals given).
- Plain words: no tool, field or check names, no codes or tags from tool results (such as [TEST]), no
  internal terms (benchmark cohort, episode, lane, ledger, scan), no acronyms ("target cost per lead", not
  "target CPA"; "return on ad spend", not "ROAS"; "Performance Max", not "PMax"). Sources are described ("your account's monthly figures"), never as tools.
  The notes are "your account notes", never the file name, also in the opening line. The one source name kept is "the AdTAO outcome ledger" in the internal version.
- Show misses as plainly as wins.
- American English unless the user or the notes use another.
- Nothing is sent. The user sends it.

## Budget

9 calls; up to 11 with conversion value, a saved budget or an experiment; 12 or 13 for the internal version.

## Tools

`get_account_intelligence`, `get_monthly_performance_comparison`, `gaql_search`, `metrics_budget_pacing`,
`get_performance_vs_benchmark`, `get_changelog`, `archetype_diagnosis`, `get_experiment_readout`,
`get_market_comparison`, `get_change_provenance_benchmark`.
