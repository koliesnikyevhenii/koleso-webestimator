# Calibration

Two parts: how fast the **agent** writes each kind of code, and the **non-shrinking buckets** that sit
around it. The numbers come from ~300 tickets across 10 Umbraco projects (versions 10–17, Razor + Vue/Nuxt
front ends, Hebrew-first multilingual sites), comparing estimates, git activity and logged time. Treat them
as starting points and replace them with your own calibration log as it grows.

---

## What the history showed

| Observation | Evidence (anonymised) | Rule it produces |
|---|---|---|
| Large work estimated in human developer-days overshot ~4× | A schema/SEO feature: 48–68 h estimated, ~13.5 h logged. A 13→17 upgrade: ~57 dev-days planned for code, ~7–8 active days in git. A 16→17 upgrade: code-complete in 2 days | Size coding at agent speed; never "human days + contingency" |
| Small "2–3 h" tickets undershot 2–3× | 2–3 h estimated, 6.3 h logged — the overrun was deploy, uSync import and a missed step on Dev | Apply the small-ticket floor (ship overhead) |
| 78% of tickets were finished in one active day; only ~2% took 4+ days | git, 290 ticket keys | Most single tickets are S; anything bigger needs a reason |
| Phased features with a reviewed plan ran at ~3–5 phases per day | A 10-phase feature in ~3 active days | Phases are cheap; plan rewrites and real-data runs are not |
| Code that writes CMS data took repeated deploy-verify rounds | One bug needed 4 PRs, each deployed to Dev before the next cause surfaced (block value order → JSON escaping of non-Latin text → picker value shape) | +1–2 verification rounds for code that writes content |
| Imports of client files into culture-variant content had the widest variance | 10 active days over 6 weeks, 24 plan revisions | ×2–3 range, budget real-file iterations |
| Front-end pages against a design were followed by a QA wave | 5–15 tickets per page or lobby, about a third closed as "not a bug" | QA-wave bucket for every designed page |
| Calendar time was set by clarification, not by hours | ~3 h of work open for 23 days; 12.5 h spread over 6 weeks | Report calendar separately |
| Each client answer set triggered a re-estimate request | Clients answer the questions, then ask "please confirm the estimate" | Questions with stated defaults; delta re-estimates |
| Upgrades: code was fast, the tail was not | Regression runs, runbooks, prod DB rehearsals and post-deploy fixes continued for weeks | Upgrade-tail bucket ≥ upgrade code |
| Mid-size estimates hit when scope was fixed in advance | 2 d estimated → 16 h logged; 3 h → 2.45 h | Fixed scope → narrow ranges are fine |

---

## Agent coding rates (Umbraco / .NET stack)

Hours of **agent work** for a sub-task with **high prior art** and a **clear spec**. Add review separately.

| Work type | Agent time | Notes |
|---|---|---|
| Doctype / element type / data type + uSync config | 0.25–0.75 h | Config files are mechanical; the cost is in getting aliases, compositions and allowed children right |
| Razor view/partial for a new page against a design | 0.5–1.5 h | Plus SCSS; plus the QA wave bucket |
| SCSS change (single component, LTR+RTL) | 0.15–0.5 h | If compiled CSS is committed, include the recompile |
| Block List / Block Grid element + its rendering | 0.5–1 h | |
| Vue island (new component + API call + mount) | 1–2.5 h | No test tooling on many Vue islands → manual check |
| Headless page (Nuxt/Next) consuming the Delivery API | 1–2.5 h | SSR errors may only show on the deployed environment |
| API controller / surface controller endpoint | 0.25–0.75 h | Body binding with System.Text.Json can fail silently — test the real request |
| Back-office extension (Bellissima Lit/TS): property editor, workspace view, entity action, dashboard | 1–3 h | Needs a manifest, a build and a manual check in the back office |
| Back-office extension on AngularJS (Umbraco ≤13) | 1–3 h | Legacy; low training-data coverage for edge cases |
| Examine index field / search query change | 0.5–1.5 h | Rebuild the index on each environment |
| Background job (Hangfire/recurring) | 0.5–1.5 h | Behaviour differs with no HTTP context / culture; test on real data |
| Umbraco migration / custom table | 0.5–1.5 h | Plus a rollback note |
| One-time content import (clean, structured source) | 1–3 h | Clone an existing importer if there is one: often same-day |
| Import of client-supplied files into culture-variant content | 3–8 h | Widest variance; ×2–3 range; real files break the contract |
| Third-party script / consent / analytics integration | 1–3 h | Often verifiable only on the real domain |
| External API integration (documented) | 1–3 h | Poor docs ×1.5–2 |
| Unit tests for a feature (xUnit + Moq) | 30–50% of the feature's coding | |
| Playwright e2e for one flow | 0.5–1.5 h | Needs a running site |
| Investigation / root cause (timeboxed) | 0.25–1 h | Common outcomes: not a bug, already built in, upstream CMS bug |
| Version upgrade — code only (one major) | 1–3 active days for a medium site | The tail is separate and larger |

### Adjustments (stack on the sub-task)

| Factor | Adjustment |
|---|---|
| High prior art (near-identical pattern to copy) | −20% |
| No prior art | +40% |
| Partial spec | +30% |
| Vague spec | +80%, and escalate (see honesty rules) |
| Legacy AngularJS back office / old Umbraco major | +20% |
| No strongly typed models (dynamic property access) | +10% |
| Monorepo / several build pipelines (Vue + back-office + SCSS + .NET) | +15% |

### Review time

Default **30–50% of agent coding** for production code (C#, Razor, TS); **15–25%** for styles, configs and
tests. Review is human time and does not get faster with the agent.

---

## Non-shrinking buckets

These are human or wall-clock work. They do not shrink because an agent writes the code. Add each that
applies as its own line.

| Bucket | Default size | Applies when |
|---|---|---|
| **Ship overhead, per environment** | 0.25–0.75 h per env | Every ticket that reaches a shared environment. Build, deploy, uSync import, re-save, smoke check |
| **Manual content / dictionary steps** | 0.1–0.25 h per item per env | Dictionary items (often create-only on import), content re-saves, media, settings nodes |
| **Real-CMS verification round** | 0.5–1.5 h per round; budget 1–2 rounds | Code writes content, blocks, media or per-culture values; jobs without HTTP context; anything `platform-risks.md` lists |
| **Owner local run + QA handover** | 0.5–1 h | Every feature; a written handover for QA |
| **QA wave (designed front end)** | 25–50% of the FE coding, min 1 h per page | Pixel-checked pages; expect ~⅓ of reports to be "not a bug" that still need a reply |
| **Multilingual / RTL matrix** | 0.1–0.2 h per culture × breakpoint for UI work | Each RTL culture adds its own layout check |
| **Clarification round** | 0.25–0.5 h effort per round (+ calendar wait) | Each set of questions → answers → re-estimate |
| **Upgrade tail** | ≥ the upgrade code itself | Regression pass, runbook, prod DB rehearsal, post-deploy fixes, editor-reported regressions |
| **Process overhead** | 10–20% of the feature | If the team writes a plan, reviews it, logs phases and writes verification docs |

### Small-ticket floor

A ticket that must reach one shared environment costs **at least 0.75–1.5 h** (code + review + ship overhead
+ QA handover), no matter how small the diff. With two or more environments, add the per-env overhead for each.

### Batching

Ship overhead, uSync imports and QA handovers are shared when several items ship together. In batch mode,
count them once per release, not once per item.
