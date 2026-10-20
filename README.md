# AdTAO Operator skills

Google Ads skills that run on AdTAO: they judge an account against evidence from many advertisers,
settled outcomes of past changes and its own market, and they change nothing until you approve it
in AdTAO.

## Install

**Claude Code:** `/plugin marketplace add adtaoai/google-ads-adtao-mcp-skills`, then
`/plugin install adtao-operator@adtao`. Or copy `skills/` into `.claude/skills/` and `.mcp.json` into
your project. The plugin connects to one server only:
`https://mcp.adtao.io/mcp`. Sign in to AdTAO when your AI app asks.

**Claude Desktop and others:** add the AdTAO connector, then paste a skill's `SKILL.md` into the
conversation or a project's instructions.

## Skills

| Skill | Use it when |
|---|---|
| `adtao` | Start here; routes any Google Ads request |
| `adtao-setup` | First time on an account, or when goals change |
| `adtao-waste-hunter` | Wasted spend in search terms and placements |
| `adtao-tracking-truth` | Are the conversion numbers real |
| `adtao-launch` | Build a new Search campaign from evidence, created paused |
| `adtao-competitors` | Who competes, what changed among them, angles to test |
| `adtao-client-report` | The monthly report for a client, as a draft you edit |
| `adtao-pmax-guard` | What AI Max, Performance Max and Google-made assets are doing |
| `adtao-upgrade-skill` | Turn another Google Ads skill into an AdTAO one |
| `adtao-author-skill` | Write a new AdTAO skill from a one-line job |

Every skill follows `HOUSE_RULES.md`: where each number comes from, which tools it uses, how many
calls it makes, and how a change is approved.

## Changes need you

A skill stages a change. You approve it on a page in AdTAO that lists exactly what will run, and
read a one-time code back to your AI app. Without that code nothing reaches Google Ads.
