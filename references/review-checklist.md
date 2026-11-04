# Review checklist — checking an existing estimate

Use this in **Review** mode: someone (a person, or an earlier run) has already produced an estimate, in a
spec, a plan, a ticket comment or a document, and you are asked to check it. Do not re-estimate from scratch
unless asked. Find what is wrong with the estimate in front of you.

## 0. Collect every version

- The estimate document (spec / plan section), **from the branch or commit it lives on**, not a local
  draft. Check that the file you read is the latest committed version.
- The client-facing copy (ticket comment, email). It often differs from the document.
- The code base it claims as evidence (a commit or branch). Note whether the code has moved since.

Record which versions you compared.

## 1. Arithmetic

- Every subtotal equals the sum of its rows (low and high separately).
- The total equals the sum of the subtotals.
- The client-facing rows add up to the client-facing total. If the total is rounded, it is rounded
  **outward** (low down, high up) and the rounding is not larger than 30 min at each end.
- The document and the client copy agree. If they differ, say which one is right.

## 2. Basis and confidence

- The basis is stated (plan-based / spec-based / quick / mixed) and matches what actually exists.
- Confidence matches the modifiers in `honesty-rules.md`. Typical over-claims:
  - "High" while client questions are still open;
  - "High" with a data-type change on stored values that has not been checked;
  - "High" while the top risk says more rounds may be needed.
- The range spread matches the confidence level.

## 3. Structure

- Coding and review are separate; review is roughly 30–50% of production coding (15–25% for styles,
  configs, tests). Flag anything outside that, with the hours it would move.
- No human developer-days, no flat contingency line.
- Every non-shrinking bucket from `calibration.md` is present or skipped with a reason.
- With a spec: every requirement maps to a line; lines with no requirement are flagged as scope additions.
- Options are priced separately and not hidden in the total.
- Every open question has a default, and the estimate says it assumes the defaults.
- Calendar dependencies are separate from effort.

## 4. Facts

- Re-check the key facts against the current code and schema: aliases, data types, where values are read
  from, what a job does, what exists or does not exist.
- Re-check claims about environments (settings, instances, pipelines) against whatever records exist. Mark
  any claim you could not verify.

## 5. Risks the estimate may miss

Go through `platform-risks.md` for the work types involved. Typical misses:
- data-type or editor swaps on properties that already hold values;
- background jobs reading new property types outside an HTTP request;
- a second clarification round, or a design arriving later and starting a QA wave;
- per-environment manual steps (dictionary, re-saves, content fixes).

## 6. Output

```
## Estimate review: [task]

**Compared:** [document @ commit] vs [client copy] vs [code @ commit]
**Verdict:** holds / holds with internal fixes / needs a revised client number

### What holds
| Check | Result |
|---|---|

### Findings
1. [finding] — impact: [hours / confidence / wording] — fix: [what to change]

### Recommended changes
- Document: [...]
- Client copy: [only if the number or a commitment changes]
```

Rules:
- Separate **internal fixes** (arithmetic, confidence, wording in the document) from **changes the client
  must see** (a different total, a new assumption). Do not suggest re-posting a number to the client unless
  it actually changes.
- If you change the document, add a dated revision entry in it that lists what changed and why.
