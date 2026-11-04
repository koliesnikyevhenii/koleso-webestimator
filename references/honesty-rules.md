# Honesty rules

Read before writing any number. These are not optional.

## Rules

1. **Ranges, never points.** "2–3 h", not "2.5 h". Minimum spread: High confidence ±20%, Medium ±40%,
   Low ±60% or more.

2. **One unit: hours.** Do not mix hours, "0.1 day" and developer-days in one estimate or across estimates
   for the same client. Days hide the agent/human distinction.

3. **No human developer-days + contingency.** Build the number bottom-up from agent sub-tasks, review and the
   non-shrinking buckets. A flat "+15–20% contingency" line is a sign the estimate was not decomposed.

4. **Never drop the work around the code.** If a non-shrinking bucket is left out, say why ("ships with the
   next release; no separate deploy"). Small tickets are underestimated precisely because these are left out.

5. **Effort and calendar are separate.** Waiting for answers, content, vendors or QA is calendar time. Do not
   inflate effort to cover it, and do not let it disappear either — state it.

6. **Verify before you claim.** Before writing that something exists, does not exist, or is built in, check
   the code, the uSync/schema files or the CMS docs. A wrong claim costs a full client round trip and
   credibility.

7. **Questions come with defaults.** Every open question is numbered and states the answer the estimate
   assumes ("Q3 — Should mobile get its own image? Assumed: no, same image cropped"). The estimate is valid for
   the defaults; a different answer gets a delta.

8. **Price options separately.** Anything optional or speculative ("also apply to Events pages", "export to
   Excel") is its own line with its own range, not hidden in the total.

9. **Do not underestimate to please, or overestimate to be safe.** If the client hopes for "a few hours" and it
   is 10–14 h, say 10–14 h and show the lines that drive it. If the honest number is small, give the small
   number — padding destroys trust as surely as overruns do.

10. **Flag scope changes, do not absorb them.** When a decision widens the scope, name it and price it.

11. **Name the top risk** in one sentence: the single thing most likely to move the number.

## Confidence

| Level | When |
|---|---|
| High | Codebase read, clear spec, strong prior art, no CMS-data writes, no external party |
| Medium | Codebase read, but partial spec, or some CMS-data writes, or one external dependency |
| Low | Codebase not read, vague spec, client files to import, third-party verification only on the real domain |

Modifiers: prior art found +1; codebase not read −1; vague spec −1; code writes culture-variant or block data
−1; depends on client-supplied content −1; similar task recorded in the calibration log with a known actual +1.
Confidence never goes above High or below Low; if several −1s stack below Low, say **"Reliability: very
low — clarify before committing"**.

## Escalate before estimating

Ask first, with an order-of-magnitude range if useful, when:
- The ticket is under two sentences with no acceptance criteria.
- The answer to one question changes the total by more than 2×.
- A data import will touch production content.
- The expected deadline is more than 3× below your first estimate.
- Ownership crosses teams or pipelines you cannot see.

## Too big

If the total is over 40 h: say XXL, name the 2–3 lines driving it, propose milestones and estimate each. Do
not publish one monolithic number.
