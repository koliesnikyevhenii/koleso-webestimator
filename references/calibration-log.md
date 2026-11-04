# Calibration log

The numbers in this skill are starting points. They get accurate only when estimates are compared with what
actually happened. Keep a log and read it in Step 0.

## Where to keep it

Pick one and stay consistent:
- Per project: `docs/estimates/calibration-log.md` in the repository, or
- Per person: a file in your agent's memory/notes folder.

Do not put client names, people or ticket contents in a log that will be shared publicly.

## Entry format

One row per ticket, written when the estimate is given and completed when the work is done.

| Date | Ticket | Work type | Estimated effort | Calendar estimate | Actual effort | Actual calendar | Rounds (clarif. / deploy-verify / QA) | What moved it |
|---|---|---|---|---|---|---|---|---|
| 2026-01-15 | ABC-123 | Razor page vs design | 4–6 h | 1 week | 5 h | 2 weeks | 2 / 1 / 6 QA tickets, 2 not-a-bug | Client added a second page type |

"Actual effort" sources, best first: your own time tracking; logged time on the ticket (note whether it is the
whole team's total); git active days as a rough lower bound. Say which source you used.

## Recalibrating

Every ~10 completed entries for a work type:
- Compute actual ÷ estimate (mid-points) per work type and per bucket.
- If a ratio is consistently outside 0.7–1.4, adjust that row in `calibration.md` and note the date.
- If misses come from one bucket (e.g. QA waves), adjust the bucket, not the coding rates.
