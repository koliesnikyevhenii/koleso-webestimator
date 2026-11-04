# Calibration log

The numbers in this skill are starting points. They get accurate only when estimates are compared with what
actually happened. Keep a log and read it in Step 0.

## Where to keep it

Pick one and stay consistent:
- Per project: `docs/estimates/calibration-log.md` in the repository, or
- Per person: a file in your agent's memory/notes folder, or a notes app (e.g. an Obsidian vault).

If the user's instructions (CLAUDE.md, memory) name a log location, **write to it automatically** after every
estimate, re-estimate and review — do not just offer. Otherwise offer to start one.

Do not put client names, people or ticket contents in a log that will be shared publicly.

## Entry format

One row per ticket, written when the estimate is given and completed when the work is done. Record both
numbers when they differ: **what the human sent to the client** and **what the skill produced**. The gap
between them is data too.

| Date | Ticket | Work type | Sent to client | Skill estimate | Actual effort (source) | Actual calendar | Ratio | Rounds (clarif. / deploy-verify / QA) | What moved it |
|---|---|---|---|---|---|---|---|---|---|
| 2026-01-15 | ABC-123 | Razor page vs design | 4–6 h | 5–8 h | 5 h (own time) | 2 weeks | 1.0 | 2 / 1 / 6 QA tickets, 2 not-a-bug | Client added a second page type |

**Ratio** = actual ÷ midpoint of the number sent to the client (add a second ratio for the skill's number
when they differ). ≈1.0 is accurate; above 1.4 is an underestimate; below 0.7 is an overestimate.

"Actual effort" sources, best first: your own time tracking; logged time on the ticket (note whether it is the
whole team's total); git active days as a rough lower bound. Say which source you used.

## Recalibrating

Use **Calibrate** mode (see `SKILL.md`). In short, every ~10 completed entries for a work type, or at the end
of a trial period:
- Compute actual ÷ estimate (mid-points) per work type and per bucket, for both the sent and the skill number.
- If a ratio is consistently outside 0.7–1.4, adjust that row in `calibration.md` and note the date.
- If misses come from one bucket (e.g. QA waves), adjust the bucket, not the coding rates.
- If the human kept overriding the skill in one direction and the human was right, the skill is wrong there.
- Fewer than ~3 completed entries for a work type is an anecdote, not a calibration: note it, do not change
  the rates.

Record every change made to the skill's numbers in a changelog entry (date, what changed, which log rows
justified it), so later reviews can see whether the change helped.
