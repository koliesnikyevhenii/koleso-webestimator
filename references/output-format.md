# Output format

Produce two parts. The internal breakdown is for the developer; the client reply is what gets posted on the
ticket. Keep the client reply free of internal tooling names (agents, prompts, models).

---

## Part 1 — Internal breakdown

```
## Estimate: [task]

**Basis:** Plan-based — [documents read] / Quick estimate — no spec or plan found (looked in: …) / Mixed
**Effort:** X–Y h   **Calendar:** [e.g. "1–2 weeks, gated on Q1–Q3"]   **Size:** [S/M/L/XL]
**Confidence:** High / Medium / Low — [one sentence on what drives it]
**Stack:** [Umbraco version, .NET, front end, back office tech, cultures, environments]
**Prior art:** [files/features it copies]

### Coding (agent) + review
| Sub-task | Agent | Review | Prior art | Note |
|---|---|---|---|---|
| ... | 0.5–1 h | 0.25 h | High | ... |

### Work that does not get faster
| Bucket | Effort | Why it applies |
|---|---|---|
| Ship overhead (Dev, Stage) | ... | ... |
| Manual dictionary / content steps | ... | ... |
| Real-CMS verification rounds | ... | ... |
| Owner run + QA handover | ... | ... |
| QA wave | ... | ... |
| Multilingual / RTL matrix | ... | ... |
| Skipped on purpose | — | [bucket: reason] |

### Spec / plan coverage (plan-based only)
| Requirement / criterion | Covered by | Estimate line |
|---|---|---|
| ... | Phase 2 | 2.1, 2.3 |
| [not covered by any phase] | — | flagged |

### What would firm this up (quick estimate only)
- [questions a spec/plan must settle; areas not pinned down]
- Next step: write a feature plan, then re-estimate from it. Expected direction: [up/down, why]

### Calendar dependencies
- [what elapsed time waits on]

### Assumptions
- [each one; if it is wrong, the number is wrong]

### Facts verified
- [claims checked in code/schema before replying]

### Top risk
- [one sentence]
```

---

## Part 2 — Client-facing reply

Short, in hours, ranges throughout, questions answerable one by one.

```
Estimate for [task]: **X–Y hours**.
[Quick estimate only:] This is a preliminary estimate of the visible scope; it will be refined once the
detailed specification / implementation plan is ready.

| # | Item | Hours |
|---|---|---|
| 1 | [requirement in the client's words] | 1–1.5 |
| 2 | ... | 0.5–1 |
| 3 | Deployment, environment setup and QA support | 0.75–1.25 |
|   | **Total** | **X–Y** |

Options (not included above):
- [option] — +A–B h

Questions — the estimate assumes the default shown:
1. [question]? *Assumed: [default].*
2. ...

Assumptions: [short list]
Risks: [top risk in plain language]
Timing: [what the start or delivery date depends on]
```

Notes:
- Put the work around the code in the client table as one or two plain lines ("Deployment, environment setup
  and QA support"); the detailed buckets stay internal.
- If the client asks whether a limit or a simpler variant would reduce the hours, answer with a priced
  alternative, not a general "yes".
- After answers arrive, reply with a **delta**:

```
Updated after your answers:
- Q2 (also Events pages): +1–1.5 h
- Q4 (no mobile image): −0.5 h
Revised total: A–B h (was X–Y h).
```

---

## Batch

```
## Batch estimate: [epic / spec]

| # | Item | Layer | Effort | Size | Confidence | Top risk |
|---|---|---|---|---|---|---|
| 1 | ... | BE | 1–2 h | S | High | ... |
|   | Shared: deploy, uSync, QA handover (per release) | — | ... | | | |
|   | **Total** | | **X–Y h** | | | |

Items needing clarification: ...
Dependencies: ...
Suggested order: ...
```
