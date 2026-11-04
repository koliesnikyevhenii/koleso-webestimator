# koleso-webestimator

> Agent Skill for estimating Umbraco/.NET web tasks when an AI agent writes the code. It sizes coding at agent speed and adds the work that does not speed up: deploys, uSync and dictionary steps, verification on a real CMS, QA waves and client clarifications. Calibrated on ~300 real tickets.

An [Agent Skill](https://docs.claude.com/en/docs/claude-code/skills) for estimating Umbraco / ASP.NET Core web
tasks when **an AI coding agent writes the code and a developer reviews it**.

It reads the codebase, sizes the coding at agent speed, and then adds the work that does not get faster:
deploys, uSync and dictionary steps on each environment, rounds of verifying on a real CMS instance, QA
waves after design work, and client clarification. Calendar time stays separate from effort.

## Why

The skill was calibrated against ~300 real tickets across 10 Umbraco projects (versions 10–17; Razor, Vue
and Nuxt front ends; multilingual, Hebrew-first RTL sites). The history showed two opposite errors with one
cause:

- **Large work estimated in human developer-days ran ~4× high.** A planned 57-day upgrade took 7–8 active
  days of code. A 48–68 h feature logged 13.5 h.
- **Small "2–3 h" tickets ran 2–3× low.** The code was fine. The deploy, the uSync import, a missed step on
  Dev and a QA bug the next day were never in the number.

Neither error was in the code. Both were in the work around it. This skill makes that work explicit.

## What it does

1. Looks for a spec (`spec.md`, `docs/spec/`) and a feature plan (`docs/plans/`). If they exist, it reads them in
   full and estimates per plan phase, mapping every requirement to a line. If not, it says so and gives a
   quick estimate of the visible scope, with wider ranges and a suggestion to plan first.
2. Detects the stack: Umbraco version, back-office technology (AngularJS or Bellissima), front end, uSync
   settings, cultures, tests and environments.
3. Reads the closest existing code, and **verifies factual claims** before making them.
4. Breaks the coding into 15 min – 2 h agent sub-tasks and adds review time.
5. Adds the buckets that do not shrink: ship overhead per environment, manual content and dictionary steps,
   real-CMS verification rounds, the owner's local run and QA handover, QA waves, the multilingual/RTL
   matrix, clarification rounds and the upgrade tail.
6. Keeps calendar dependencies separate.
7. Writes an internal breakdown and a reply ready to post for the client: hour ranges, options priced
   separately, and numbered questions that each state the default answer.

Modes: single task, batch (spec / epic / backlog), delta re-estimate after answers, timeboxed investigation.

## Install

Claude Code, personal:

```bash
git clone https://github.com/koliesnikyevhenii/koleso-webestimator ~/.claude/skills/koleso-webestimator
```

Claude Code, one project (shared with the team through git):

```bash
git clone https://github.com/koliesnikyevhenii/koleso-webestimator .claude/skills/koleso-webestimator
```

With the skills CLI:

```bash
npx skills add koliesnikyevhenii/koleso-webestimator
```

Start a new session afterwards. Use it by asking ("how long would this take?", "estimate TICKET-123") or
directly with `/koleso-webestimator <task>`.

## Files

| File | Purpose |
|---|---|
| `SKILL.md` | Workflow and modes. The only file loaded automatically |
| `references/calibration.md` | Evidence, agent coding rates, adjustments and non-shrinking buckets |
| `references/patterns.md` | Reference times for Umbraco, Razor, Vue/Nuxt, back-office, backend, data and upgrade work |
| `references/platform-risks.md` | CMS behaviours that pass tests but fail on a real instance |
| `references/honesty-rules.md` | Mandatory rules, confidence levels and escalation |
| `references/output-format.md` | Internal breakdown, client reply, delta and batch formats |
| `references/calibration-log.md` | How to record estimate vs actual and recalibrate |

## Calibrate it to yourself

The rates are starting points from one team's history. Keep the calibration log
(`references/calibration-log.md`). After ~10 finished tickets of a work type, adjust that row. If your
misses come from one bucket, such as QA waves, adjust the bucket, not the coding rates.

## Credits

Inspired by [ecappa/web-dev-estimation](https://github.com/ecappa/web-dev-estimation) by Eric Cappannelli,
which introduced estimates calibrated for an AI agent rather than a human. This skill is a separate
implementation focused on Umbraco/.NET, with its own calibration data. No text from that project is
reused.

## License

MIT, see [LICENSE](LICENSE).
