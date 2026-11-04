# Reference patterns

Agent-time references for common work on Umbraco / ASP.NET Core sites. Use them to cross-check the sub-task
sizes from `calibration.md`. Times are agent coding only, with high prior art; add review and the
non-shrinking buckets separately.

## T-shirt sizes (total effort, all buckets included)

| Size | Effort | Typical shape |
|---|---|---|
| XS | < 1 h | One-file fix that ships with something else; pure investigation |
| S | 1–3 h | Single ticket reaching one or two environments |
| M | 3–8 h | New page or component with schema + front end + tests, one release |
| L | 8–20 h | Multi-page feature, new back-office extension, data import with real files |
| XL | 20–40 h | Epic slice, version upgrade of a medium site including the tail |
| XXL | > 40 h | Split into milestones before estimating |

## Content model & CMS

| Pattern | Agent time | Notes |
|---|---|---|
| New doctype with 5–10 properties, template, uSync | 0.5–1 h | Watch alias collisions with compositions |
| Rename / retire a doctype or data type | 0.5–1.5 h | Removing a uSync file deletes nothing on import — needs an explicit delete stub or a migration |
| Change a property to vary by culture | 0.5–1 h | Plus a content re-save on every environment |
| New Block List element + view | 0.5–1 h | Block thumbnails / labels in the back office are separate |
| Restrict a picker (allowed types, file types) | 0.25–0.75 h | |
| Dictionary items for a feature (3 languages) | 0.25–0.5 h | Plus a manual edit per environment if import is create-only |

## Server-rendered front end

| Pattern | Agent time | Notes |
|---|---|---|
| New page (Razor + SCSS) against Figma, desktop + mobile | 1–2 h | QA wave bucket applies |
| Lobby / listing page with paging or "load more" | 1.5–3 h | Culture header / query must reach the partial endpoint |
| Share bar, meta tags, Open Graph per page type | 0.5–1.5 h | Each extra page type ≈ +0.25 h |
| SCSS tweak to match a measured design value | 0.15–0.5 h | Check whether the style is shared by another site/page first |
| Recompile committed CSS (LTR + RTL) | 0.1–0.25 h | |

## JS front end

| Pattern | Agent time | Notes |
|---|---|---|
| Vue 3 island: component + API + mount from Razor `data-*` | 1–2.5 h | |
| Filter page with router and server filtering | 2–4 h | |
| Nuxt page over the Delivery API | 1–2.5 h | SSR-only failures appear after deploy |
| Overlay / lightbox with keyboard and focus handling | 1–3 h | Focus bugs show up only after paging edge cases |

## Back office

| Pattern | Agent time | Notes |
|---|---|---|
| Bellissima property editor (Lit/TS) + manifest | 1–2.5 h | |
| Workspace view / info app | 1–2 h | |
| Entity action with a modal | 1–2 h | |
| Dashboard (incl. iframe to an existing tool) | 0.5–1 h | |
| Tiptap toolbar / extension | 1–3 h | RTE behaviour (RTL, styles) needs manual checking |
| Port an AngularJS plugin to Bellissima | 2–6 h each | Read the old plugin fully; behaviour is undocumented |

## Backend

| Pattern | Agent time | Notes |
|---|---|---|
| API controller endpoint + DTO + validation | 0.25–0.75 h | |
| Content finder / URL provider change | 0.5–1.5 h | Routing changes need checks per culture and per domain |
| Examine: index a new field + query it | 0.5–1.5 h | Rebuild indexes per environment |
| Recurring job (move/archive content by date) | 0.5–1.5 h | Test with real culture-variant data |
| Email template (Razor) + send | 0.5–1 h | SMTP config per environment |
| Custom table + migration + repository | 0.5–1.5 h | |
| CSV/Excel export from the back office | 0.5–1.5 h | |

## Data

| Pattern | Agent time | Notes |
|---|---|---|
| One-time import from a clean JSON/CSV, existing importer to clone | 0.5–1.5 h | Often same day |
| New importer for client files into culture-variant nodes | 3–8 h | ×2–3 range; iterate on the real file |
| QA seed content on an environment | 0.25–1 h | Editors' QA depends on it |

## Upgrades

| Pattern | Agent time | Notes |
|---|---|---|
| One Umbraco major, small site, code only | 0.5–1.5 days | |
| One Umbraco major, medium site with custom back-office plugins, code only | 2–5 days | Back-office plugin rewrites dominate on 13→14+ |
| Upgrade tail (regression, runbook, rehearsal, post-deploy fixes) | ≥ the code | Never omit |

## Known agent failure patterns on this stack

Add your own as you meet them.

| Failure | Symptom | Mitigation in the estimate |
|---|---|---|
| Green tests, broken view | Razor is often not compiled at build, so tests pass while a `.cshtml` fails | Include a Razor-compiling build or a page load in verification |
| Fact asserted without checking the schema | "That field does not exist" — it did | Verify claims in uSync/content-type configs before replying |
| Assumed persistence semantics | Values saved in the wrong shape are silently dropped or not published | Real-CMS verification round |
| Culture-less background code | Culture-variant nodes skipped in jobs | Test jobs on variant content |
| Serializer differences | Non-Latin text escaped or values reordered when written outside the CMS's own serializer | Use the CMS serializer; verify the stored JSON |
| Shared styles changed by accident | A fix for one page restyles another site sharing the stylesheet | Check style ownership; ask before changing shared styles |
