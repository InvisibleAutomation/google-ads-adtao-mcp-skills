---
name: adtao-author-skill
description: Use when the user wants a new Google Ads skill written for AdTAO from a one-line description of the job. Writes a SKILL.md in the same format as the AdTAO skills, or says plainly that a plain Google Ads connection does the job as well and no AdTAO skill is needed. Requires the AdTAO MCP server.
---

# Author a skill on AdTAO

## Steps

1. **Restate the job** in one line: who runs it, on what, and what they get at the end. Ask one
   question only if the job cannot be written without the answer.
2. **Already covered?** If an AdTAO skill does this job (waste hunter, tracking truth, competitors,
   client report, PMax guard, setup), say so and stop, or write a thin skill whose steps say "follow
   that skill" and add only what differs (the output format, a threshold, a filter).
3. **The Claude test.** Does AdTAO add something a plain Google Ads connection cannot: evidence from
   many advertisers, settled outcomes of past changes, comparisons with similar accounts, or changes
   approved before they run? Call `adtao_capabilities` to check what AdTAO offers. If the answer is
   no, say so in two lines and do not write the skill.
4. **Choose the tools** from the tool lists of the AdTAO skills closest to the job, fewest that do
   it. Every tool the new skill names goes in its Tools list.
5. **Write the SKILL.md** in the house-rules format: frontmatter (`name` in lower case with hyphens,
   `description` starting "Use when", ending "Requires the AdTAO MCP server."), numbered steps, a
   **Rules** section, a **Budget** line (calls per run) and a **Tools** list. Any change goes through
   the approval path in house rules §6.
6. **Add one paragraph for the user**, not in the file: why a plain Google Ads connection is not
   enough for this job, naming only what step 3 found.

## Rules

- The answer opens with the one-line restatement from step 1, before anything else.
- If step 2 finds an AdTAO skill or area that already does most of the job, say so in the answer's
  first lines and write only the thin version that points to it; never a full parallel skill.
- In what the user reads, name no tools and no codes: say "what AdTAO offers", "what happened after
  the same change on other accounts", "cost per lead". Tool names belong only inside the SKILL.md.
- The paragraph on why a plain Google Ads connection is not enough is tagged [TEST]: it is a claim
  until a run of the new skill shows it.

- Describe tools only by what their own descriptions and the house rules say. A claim that the new
  skill does better than a plain connection is [TEST] until a run shows it.
- No account numbers, internal names or links in the file.
- Keep it short: a skill longer than about 80 lines is two skills.

## Budget

One `adtao_capabilities` call; none otherwise. It writes a file.

## Tools

`adtao_capabilities`.
