# Platform risks that cost extra rounds

These are the places where code that passes its unit tests still fails on a real Umbraco instance. Each one
justifies at least one **real-CMS verification round** in the estimate (see `calibration.md`). Check the
target version — several are version-specific, and later versions may fix them.

## Content persistence

- **Writing property values from code** (services, jobs, imports): the stored JSON shape must match what the
  property editor expects. A wrong shape is often silently ignored, not rejected.
- **Block List / Block Grid values**: layout and content data must agree; order and keys matter.
- **Media picker values**: the stored shape differs between picker generations; a string vs an array is a
  classic silent failure.
- **Serializer**: writing JSON with a different serializer than the CMS uses can escape non-Latin text or
  reorder values. Prefer the CMS's own serializer.
- **Per-culture save and publish**: publishing "all cultures" via a wildcard is not accepted by every
  version; culture-less queries can drop culture-variant nodes in background code.
- **Date/time properties**: precision can be lost on persistence (e.g. milliseconds), breaking comparisons
  after a cache reload.
- **Validation state in the back office**: an upstream bug can keep a new node failing validation until reload
  — know it before you blame the doctype.

## Schema sync (uSync)

- **Import on startup is usually off**: every schema change needs a manual import per environment.
- **Dictionary items are not exported on save** and may be **create-only** on import: existing values never
  update. Budget a manual edit per environment.
- **Deleting a config file deletes nothing** on import: a removal needs an explicit delete stub or a migration.
- **Arrays in environment config merge by index**: an environment override can leave a different effective
  list than you think.
- **Stale files left by file-copy deploys** can cause duplicate-key errors on import.

## Rendering and routing

- **Razor may not be compiled at build** (runtime compilation): a green build says nothing about views.
- **Culture comes from somewhere specific** (a header, the domain, a route segment): an AJAX partial that
  omits it renders the wrong language silently.
- **Hidden URL segments / custom URL providers**: routing changes need checks per culture and domain.
- **Conditional `data-*` attributes** in Razor render as empty attributes, not absent ones.

## APIs

- **System.Text.Json body binding**: an enum sent as a string can bind to an empty model with no 400.
  Filters become no-ops instead of errors.

## Back office

- **Bellissima (14+)**: extensions need a build step; the generated folder is often git-ignored and must be
  built by the deploy. A deploy that skips it ships no custom editors.
- **Upstream features land in specific versions**: some fixes are "wait for version X". An investigation can
  legitimately end there.

## Environments and deploys

- Several app instances sharing one database: cache sync can make publishes look missing on one instance.
- Two git remotes or two pipelines: a merged change may never have been deployed.
- A green front-end pipeline does not prove SSR works — check a real page after deploy.
- Locked build output (IDE / local IIS) can break local builds; build in a separate worktree.
- Third-party scripts (consent, analytics, CORS on a vendor's storage) are often only verifiable on the real
  domain.

## Front end against a design

- QA often measures the design file at a different frame width than the one implemented, or a cropped image
  layer, or another component. Expect a share of "not a bug" reports; each still needs a reasoned reply with
  measurements.
- Fonts: a web font missing a weight for one script can fall back to a different face (e.g. caps for Latin).
- Shared styles between sites or page types: confirm ownership before "fixing" a value.
