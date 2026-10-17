---
name: adtao-client-report
description: Use when the user wants the monthly report or commentary for a client's Google Ads account. Drafts it from results, goals, who changed the account and how such changes went, what AdTAO did, and how the account compares with similar ones. Nothing is sent. Requires the AdTAO MCP server.
---

# Client report

A draft for the user to edit and send themselves. It reports what happened after changes, not what
the changes caused, unless an experiment measured it.

## Steps

1. **Context.** If an `account-context.yaml` is in the working folder or was given to you, read it:
   goal, approver, and which email addresses belong to the agency and which to the client. If none was
   given, say "no account notes were provided" and that the "who changed it" section cannot split
   agency from client staff. In the draft it is "your account notes", never the file name.
2. **Draft.** Call `narrate_client_report` for the account and month (default: the last complete
   month). It returns the draft with a source on every figure.
3. **Check.** If the user wants the figures behind a sentence, `build_client_report` returns the same
   report as data. A section the tool says it could not read stays in the draft as "not available this
   month", with its reason; never fill it in.
4. **Who changed it.** For a fuller answer on AdTAO against Google's own suggestions and people's
   changes, call `get_change_provenance_benchmark`. Quote a lane's figure only when the tool gives one
   (it needs enough changes across enough accounts); otherwise say there are too few changes to compare.
5. **Edit with the user.** Keep their wording where they change it. Keep every number as the tool
   gave it, with its window.

## Rules

- "After", not "because", for any change and the result that followed it. Do not tie this account's
  own changes to the cross-account figures ("sit inside that pattern"); give them side by side.
- The cross-account comparison counts change groups (one per campaign and change window), not single
  changes: never set it against this month's change count, and do not explain a gap between them.
- Every figure about other accounts carries the number of accounts it rests on.
- Keep each count from the section that gave it: the change-history count and AdTAO's own record can
  differ by a few, and the draft says so rather than mixing them.
- No internal words in the draft: say "how changes like these went across accounts", not benchmark,
  episode, fleet, lane or ledger codes. The one source name kept on purpose is "the AdTAO outcome
  ledger", because every outcome figure must name where it comes from.
- When a figure is missing for a change type, say only that there are not yet enough changes across
  accounts to compare; do not guess why.
- Show misses as plainly as wins.
- Comparisons with similar accounts carry how many accounts they rest on; under 10, leave them out.
- American English. No internal names, no references to AdTAO's own systems beyond "AdTAO".
- Nothing is sent. The user sends it.

## Budget

1 to 3 calls: the draft, and optionally the data and the benchmark.

## Tools

`narrate_client_report`, `build_client_report`, `get_change_provenance_benchmark`.
