---
name: koleso-webestimator
description: "Estimates web tasks on Umbraco / ASP.NET Core sites (Razor, Vue or Nuxt front ends, Bellissima or AngularJS back-office extensions, uSync, Examine, Hangfire, multilingual and RTL) when an AI coding agent writes the code and a developer reviews it. Looks for a spec (spec.md, docs/spec) or feature plan (docs/plans) first and, if found, reads it in full and estimates per plan phase; if not, says so and gives a quick estimate of the visible scope. Reads the codebase, sizes the coding at agent speed, then adds the work that does not get faster — deploys, uSync and dictionary steps per environment, real-CMS verification rounds, QA waves, client clarification — and keeps calendar wait separate from effort. Use when asked 'how long', 'estimate this', 'how many hours', 'what is the effort', 'size this ticket', 'can we do this by', 'give the client a number', 'break this down', 're-estimate after the answers', or when a ticket titled 'Estimation – …' is in front of you. Also supports batch estimation of a spec, epic or backlog, delta re-estimates after scope changes, and reviewing an existing estimate ('review this estimate', 'check the estimate', 'is this estimate right', 'verify the numbers in the spec')."
license: MIT
compatibility: Needs read access to the target codebase. Works best with access to the ticket tracker (Jira/Confluence) for the spec.
metadata:
  version: "1.0.0"
---

# koleso-webestimator — agent-calibrated estimates for Umbraco / .NET

Estimates for Umbraco/.NET web work where **an AI agent writes the code and a human reviews it**.

The single most important fact behind this skill, from ~300 real tickets across 10 Umbraco projects:

> **The coding is rarely what makes an estimate wrong.** Large work estimated in human developer-days
> overshot by ~4×. Small "2–3 h" tickets undershot by 2–3×. Both errors come from the same place:
> the work around the code — deploys, uSync imports, per-environment manual steps, real-CMS behaviour
> that tests cannot see, QA waves, and client clarification rounds — was either inflated as
> "contingency" or left out.

So every estimate here has **two ledgers**:

1. **Effort** — hours someone actually works: agent coding + review, plus the buckets that do not shrink.
2. **Calendar** — elapsed time, dominated by waiting for answers, deploy windows and QA queues. Never mixed into effort.

Reference files (read when the step says so):
- `references/calibration.md` — agent-speed coding rates and the non-shrinking buckets with default sizes
- `references/patterns.md` — reference times for common Umbraco / Razor / Vue / back-office tasks
- `references/platform-risks.md` — CMS behaviours that cost extra deploy-verify rounds
- `references/honesty-rules.md` — mandatory rules and escalation thresholds
- `references/output-format.md` — the internal breakdown and the client-facing reply
- `references/calibration-log.md` — how to record estimate-vs-actual so the numbers improve
- `references/review-checklist.md` — Review mode: checking an estimate that already exists

---

## Workflow

### Step 0 — Find the spec and plan, and decide the estimate basis

Before reading code, search for planning documents about this task. Look in:
- the repository: `spec.md`, `SPEC.md`, `docs/spec/**`, `docs/specs/**`, `docs/plans/**` (files named
  `*-plan.md`, `*-design.md`, `*-analysis.md`, `*-spec*.md`), `docs/handover/**`, `docs/adr/**`, and any file
  whose name contains the ticket key or the feature's slug;
- the ticket: its description, attachments and comments, and any spec page linked from it (e.g. Confluence);
- earlier estimates for the same ticket. A previous estimate, a rejected option or an open question changes
  the number.

Then pick **one basis** for the estimate and say which it is at the top of the output:

**A. Plan-based estimate.** A spec and/or a feature plan exists.
- Read every document found **in full**, not just the headings: requirements, acceptance criteria, phases,
  file lists, open questions, revisions/deviations and review decisions.
- Estimate **per plan phase**, using the plan's own file lists and steps as the sub-task skeleton (Step 4).
- Map each spec requirement / acceptance criterion to a line of the estimate. List any requirement no
  phase covers, and any phase no requirement asks for.
- Carry the plan's open questions into the output's question list, each with its default answer.
- Check whether the plan is stale: compare what it names (files, aliases, doctypes) with the current code.
  Lines that no longer match are an assumption to flag, not something to price silently.
- Ranges can be narrower and confidence higher (see `references/honesty-rules.md`).

**B. Quick estimate (visible scope).** No spec and no plan were found.
- Say so plainly at the top: **"No spec or feature plan found — this is a quick estimate of the visible
  scope."** List where you looked.
- Estimate from the ticket text and the code only. Ranges are wider and confidence is capped at Medium.
- List what a spec or plan would have to settle for the number to firm up (the questions, the affected
  areas you could not pin down).
- Offer the next step: write a plan for the ticket, then re-estimate from it (basis A). Say in which
  direction you expect the plan-based number to move, and why.

If only a partial document exists (e.g. a spec but no plan, or a plan for a different phase), use basis A for
what it covers and basis B for the rest, and label each line.

Also: if a calibration log exists (`references/calibration-log.md` explains where), read the last entries for
similar work types.

### Step 1 — Detect the stack

Use your file-search tools (not shell-specific commands — this often runs on Windows). Record:

| Look for | Tells you |
|---|---|
| `*.csproj` → `Umbraco.Cms` PackageReference version, `TargetFramework` | Umbraco major (10/13 = AngularJS back office; 14+ = Bellissima Lit/TS) and .NET version |
| `ModelsMode` in `appsettings*.json`, generated models folder | Strongly typed models vs dynamic `IPublishedContent` |
| `uSync/` folder + uSync settings (`ExportOnSave`, `ImportOnFirstBoot`, disabled handlers) | Whether schema changes need a manual import per environment |
| `App_Plugins/*/package.manifest` vs `umbraco-package.json` + a `Client/` Vite project | Back-office extension technology |
| `package.json` files: `vue`, `nuxt`, `react`, `lit`, `jquery`; build output location | Front-end type and whether its build output is committed |
| SCSS compiler config, committed compiled CSS, `rtl`/`ltr` folders | Style pipeline and RTL duplication |
| Languages in uSync `Languages/`, `dir="rtl"` in layouts | Number of cultures and RTL verification cost |
| Test projects (xUnit/NUnit/Playwright), whether Razor is compiled at build | What a green test run actually proves |
| `Composers/`, Hangfire, Examine index config, migrations, custom DB tables | Background jobs, search, custom data |
| CI/deploy files, hosting hints (App Service, IIS, Docker) | How many environments and whether deploys are manual |

If you cannot read the codebase, say so in the output and widen every range 2–3× (see honesty rules).

### Step 2 — Read the code closest to the task

Find prior art: the nearest existing doctype, partial, controller, Vue island, back-office element or import
that does something similar. Read 2–4 of those files. **Check every factual claim you will make in the
estimate against the code or the uSync schema** — e.g. before writing "that field does not exist", search the
content-type configs. A wrong claim costs a full client round trip.

### Step 3 — Classify

| Dimension | Options |
|---|---|
| Work type | see the table in `references/calibration.md` (doctype/schema, Razor page vs design, Vue island, back-office extension, API/controller, search, background job, import of client data, third-party integration, upgrade, investigation, bug) |
| Prior art | High / Medium / Low |
| Spec clarity | Clear / Partial / Vague |
| Writes CMS data? | Does the code create, save or publish content, blocks, media or per-culture values? |
| Front end against a design? | Pixel-checked Figma work triggers a QA wave |
| Cultures × breakpoints | e.g. 3 languages (one RTL) × desktop/mobile |
| Environments it must reach | Local, Dev, Stage, Prod — and which steps are manual on each |
| External party | Client content, a vendor API, a third-party script, another team's pipeline |

### Step 4 — Decompose and size the coding at agent speed

With a plan (basis A), the plan's phases are the top-level groups and its steps/file lists are the
sub-tasks; do not invent a different breakdown. Without a plan (basis B), build the breakdown yourself.

Break the work into sub-tasks of **15 min – 2 h of agent work each**. Size them from
`references/patterns.md` and the rates in `references/calibration.md`. Then add **review time**
(default 30–50% of agent coding for production code; less for tests and styles).

**Never size coding in human developer-days and then "add contingency".** That is the ~4× overshoot.
If a reference you are using is in human time, convert it with the multipliers in `calibration.md`.

### Step 5 — Add the buckets that do not shrink

From `references/calibration.md` → "Non-shrinking buckets". Add each one that applies, as its own line,
with its own range:

- **Ship overhead per environment** (build, deploy, uSync import, dictionary/content edits, re-save, smoke check)
- **Real-CMS verification rounds** when the code writes CMS data or depends on runtime behaviour (`platform-risks.md`)
- **Owner/local run + QA handover**
- **QA wave** for front-end work against a design (triage, fixes, "not a bug" replies)
- **Multilingual / RTL verification matrix**
- **Clarification rounds** you expect before the scope is fixed
- **Upgrade tail** for version upgrades (regression run, runbook, rehearsal, post-deploy fixes)
- **Process overhead** if the team writes plans, reviews, verification docs and handovers per ticket

Apply the **small-ticket floor**: any ticket that must reach a shared environment costs at least the ship
overhead, even if the code is a one-liner.

### Step 6 — Calendar time

List what the elapsed time depends on: open questions to the client, content the client must supply,
vendor answers, deploy windows, QA availability. Give calendar time separately (e.g. "2–3 h effort;
realistically 1–2 weeks elapsed while Q1–Q4 are answered").

### Step 7 — Self-check

1. Sub-task ranges add up to the total.
2. No single sub-task is over 2 h of agent work without being split.
3. Every non-shrinking bucket was considered; the ones skipped are skipped on purpose (say why).
4. No human developer-days anywhere; one unit (hours) throughout.
5. Every "does not exist" / "already supported" claim was verified in code or schema.
6. Confidence is consistent with the modifiers in `honesty-rules.md`.
7. Total > 40 h → split into milestones and estimate each.
8. The basis (plan-based / quick / mixed) is stated at the top. With a plan, every spec requirement maps to
   a line and every plan phase is priced. Without one, the output says so and lists where you looked.

### Step 8 — Output

Use `references/output-format.md`: an **internal breakdown** for the developer, and a **client-facing reply**
(table of hour ranges, total range, separately priced options, assumptions, numbered questions each with the
default answer the estimate assumes, risks, calendar note).

Offer to add an entry to the calibration log so the actual can be recorded later.

---

## Modes

**Single** — the workflow above.

**Batch** — for a spec, epic or backlog: run Steps 0–2 once, then Steps 3–7 per item (2–5 sub-tasks each).
Output one table (item, layer, effort range, size, confidence, top risk), shared assumptions, cross-item
dependencies, a suggested order, and the non-shrinking buckets **once for the whole batch** where they are
genuinely shared (one deploy + one uSync import can carry several items; one QA wave per page, not per item).

**Re-estimate** — after the client answers questions or scope changes: show only the delta (removed lines,
added lines, revised total, which question caused it). Clients answer the questions and then ask for the
number to be confirmed — make the delta easy to approve.

**Review** — when an estimate already exists (in a spec, a plan, a ticket comment) and you are asked to check
it: do not re-estimate from scratch. Follow `references/review-checklist.md`:
- collect every version: the committed document, the client-facing copy, the code it cites;
- check the arithmetic, then whether basis and confidence match the honesty rules;
- check the structure (review share, buckets, requirement coverage, options, defaults);
- re-verify the key facts in code and schema, and look for the platform risks it misses.

Separate internal fixes from changes the client must see. Recommend re-posting a number only if it changes.

**Investigation** — for "find out why…" tickets: timebox it (typically 0.25–1 h), state what the timebox
buys, and list the likely outcomes, including "not a bug", "already built in" and "upstream CMS bug — wait for
version X". These outcomes are common and cheap; do not price an investigation as if a fix is certain.
