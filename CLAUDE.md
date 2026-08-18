# TrueFocus Mind Performance Site — Repository Instructions

These instructions apply to the entire `TFMcoder/tfp-site` repository. `AGENTS.md` and `CLAUDE.md` must remain byte-for-byte identical so Codex and Claude operate under the same rules.

## Repository identity

- Product: TrueFocus Mind performance coaching landing page.
- Repository: `TFMcoder/tfp-site`.
- Canonical branch: `main`.
- Live site: `https://coach.truefocusmind.ca`.
- Related repository: `TFMcoder/tfm-clinics`, serving `https://clinics.truefocusmind.ca`; it is a different project and is always out of scope here.
- Architecture: static HTML with no framework, build step, package manager, bundler, or static-site generator.
- Publishing layout: files are served from the repository root. Do not introduce a `/docs` publishing directory.
- Page architecture: each HTML page owns its HTML, CSS, and JavaScript in one file. There is no shared stylesheet. Preserve that architecture unless a task explicitly thaws it.
- Asset architecture: images are currently embedded as large `data:` URLs. Do not run a formatter, minifier, or broad rewrite over either HTML file; it can create an unreadable and unsafe diff.
- Asset paths added later must be relative or root-relative web paths. Never commit absolute local filesystem paths.

### Source baseline audited for this file

The pre-governance source audit was pinned to `main` commit `f9530006c4ab85745d1ca678001a835d747c8e4e`:

- `index.html` blob `53c432824a952405a8869f6129a77ef4bcd7a64e`
- `case-hungarian-experiment.html` blob `d48dbe014d7538ea07b9dc9402936f03b20055fe`
- `CNAME` blob `03a11f09e4384197d11228fd93071d4879c39ee8`

Re-read the current files before every task. Do not rely on this baseline if `main` has advanced.

## Deployment contract

GitHub Pages is the only deployment mechanism. The declared publishing source is branch `main`, folder `/` (repository root), but agents must verify the live repository setting rather than infer it from the file layout.

Before the first edit in a new working session, run:

```bash
gh api repos/TFMcoder/tfp-site/pages --jq '{status,build_type,source,cname,html_url,https_enforced}'
```

The expected source is:

```json
{
  "branch": "main",
  "path": "/"
}
```

If the endpoint reports anything else, stop and raise the conflict before editing or pushing. If the endpoint cannot be queried, do not claim the Pages source is confirmed and do not declare a publishing task complete.

A push to the configured Pages source publishes the committed files directly. Do not add npm, a lockfile, a build output directory, generated-site tooling, or a deployment workflow unless a task explicitly authorizes an architectural migration.

### CNAME rule

`CNAME` is immutable. At the audited baseline it is exactly 22 bytes and contains, with no trailing newline:

```text
coach.truefocusmind.ca
```

Never delete, move, rename, reformat, regenerate, or edit `CNAME`. Preserve its byte content. A changed or missing `CNAME` can break the custom domain.

## Freeze status and working method

Modular freeze/thaw. The site is currently FROZEN at v9-2. A module is thawed only when a task explicitly names it. Per module: plan → test → review → iterate → freeze. Two tests per milestone — a deterministic render/behaviour check, and my human judgement on whether it solves the problem. Nothing is overwritten without a stated risk assessment.

The `v9-2` freeze label is an owner-supplied governance state; it is not encoded in the current HTML.

Branch discipline:

- Treat `main` as the stable deployment baseline.
- Create a task branch from the verified current `main`; use a scoped name such as `codex/task-1-governance`.
- Do not commit directly to `main`, merge, or delete the task branch without owner review.

Current default state:

- All production HTML, CSS, JavaScript, copy, URLs, embedded assets, and deployment files are frozen.
- A task thaws only the named module and only the minimum files and lines needed for it.
- Files described as `READONLY` remain readable for pattern matching but cannot be modified.
- Task 1 thaws only `AGENTS.md` and `CLAUDE.md`.
- Task 2 remains blocked until the owner approves this file.

Before changing a thawed module, state a concise risk assessment covering:

1. Intended files and exact edit surface.
2. Frozen behavior or content that could be affected.
3. Deterministic checks that will detect regression.
4. Rollback method.

Use surgical edits. Never replace an entire HTML file to change one component.

## File map and protected boundaries

| Path | Role | Default state |
|---|---|---|
| `index.html` | Landing page, all page CSS, all page JavaScript, embedded brand/coach images, case-study grid, booking links | Frozen |
| `case-hungarian-experiment.html` | Standalone Case Report 001, its CSS, charts, canvases, data, copy, and booking link | Read-only / frozen |
| `CNAME` | GitHub Pages custom-domain binding | Immutable |
| `AGENTS.md` | Codex repository governance | Thawed only by an explicit governance task |
| `CLAUDE.md` | Verbatim mirror of `AGENTS.md` | Must match `AGENTS.md` byte-for-byte |

## Actual design system

The code is authoritative. Do not substitute remembered tokens or create a parallel token system.

### Color tokens — `index.html`, verbatim

```css
:root{
  --bg:#0A0E17;
  --bg-raise:#0E1420;
  --paper:#EDEBE3;
  --muted:#8A93A6;
  --slate:#8A93A6;
  --cobalt:#3D55FF;
  --cobalt-deep:#2E43D8;
  --cobalt-text:#8FA0FF;
  --hairline:rgba(237,235,227,.12);
  --line:rgba(237,235,227,.12);
  --glow:rgba(61,85,255,.16);
}
```

`--slate` aliases `--muted`. `--line` aliases `--hairline`. Preserve both aliases because existing markup and CSS use them.

### Color tokens — `case-hungarian-experiment.html`, verbatim

```css
:root{
  --bg:#0A0E17; --bg-raise:#0E1420; --paper:#EDEBE3; --muted:#8A93A6;
  --cobalt:#3D55FF; --cobalt-deep:#2E43D8; --cobalt-text:#8FA0FF;
  --hairline:rgba(237,235,227,.12); --glow:rgba(61,85,255,.16);
}
```

The case page intentionally does not define `--slate` or `--line`.

### Component and canvas color literals

Do not approximate or normalize these literals while pattern matching:

- Solid literals: `#fff`, `#000`, `#B9C4FF`.
- Dark navigation overlay: `rgba(10,14,23,.82)`.
- Paper-family overlays use RGB `237,235,227` at component-specific alpha values.
- Cobalt-family overlays use RGB `61,85,255` and `143,160,255` at component-specific alpha values.
- Landing-page canvas additionally uses `rgba(178,190,255,...)`.
- Case-study canvas additionally uses RGB `109,130,255` and signal baseline `rgba(138,147,166,.55)`.

Retain the exact alpha values from the adjacent component. Do not replace them with a generic opacity.

### Typography

The Google Fonts request is identical in both HTML files:

```html
<link href="https://fonts.googleapis.com/css2?family=Archivo:wdth,wght@62..125,400..900&family=Newsreader:opsz,wght@6..72,400;6..72,500&family=IBM+Plex+Mono:wght@500;600&display=swap" rel="stylesheet">
```

| Role | Exact stack / settings |
|---|---|
| Body prose | `'Newsreader',Georgia,serif` |
| Display headings and CTA text | `'Archivo',sans-serif` |
| Eyebrows, labels, metadata, chart text, footer | `'IBM Plex Mono',monospace` |
| Landing body | `font-size:1.075rem; line-height:1.68` |
| Case-study body | `font-size:1.06rem; line-height:1.7` |
| Landing `.display` | `font-weight:800; letter-spacing:-.02em; line-height:1.05` |
| Case `.display` | `font-weight:800; letter-spacing:-.02em; line-height:1.06` |
| `.eyebrow` | `font-weight:600; font-size:.72rem; letter-spacing:.18em; text-transform:uppercase` |
| `.eyebrow::before` | literal prefix `// ` in `--muted` |

Do not replace these stacks with system-font approximations. Do not add new font families.

### Layout widths

- Landing `.wrap` and `.nav-inner`: `max-width:1080px`.
- Case-study `.wrap` and `.nav-inner`: `max-width:980px`.
- Standard horizontal page gutter: `24px`.
- Mobile landing navigation gutter at `max-width:560px`: `16px`.
- The landing case grid is `repeat(auto-fit,minmax(320px,1fr))` with `24px` gap.
- The landing problem-card grid is `repeat(auto-fit,minmax(260px,1fr))` with `18px` gap.
- The case protocol grid is `repeat(auto-fit,minmax(240px,1fr))` with `14px` gap.
- The case data strip is `repeat(auto-fit,minmax(190px,1fr))`.

### Spacing and radii

There is no named spacing-scale variable in the source. Spacing is component-scoped. Do not invent a new scale or silently normalize values.

Canonical layout spacing to reuse by adjacency:

| Surface | Exact values |
|---|---|
| Landing navigation | `padding:14px 24px`; at `560px`, `12px 16px` |
| Landing hero | `padding:120px 24px 130px`; at `820px`, `88px 24px 96px` |
| Landing sections | `padding:104px 0`; at `820px`, `76px 0` |
| Landing timeline | `margin-top:70px`; `column-gap:48px`; mobile stage bottom padding `44px` |
| Landing case grid | `margin-top:48px`; gap `24px`; card padding `38px` |
| Landing problem cards | `margin-top:56px`; gap `18px`; card padding `34px 30px` |
| Landing CTA | `padding:16px 30px`; radius `5px` |
| Case hero | `padding:96px 24px 84px` |
| Case sections | `padding:88px 0`; at `760px`, `64px 0` |
| Case protocol | `margin-top:44px`; gap `14px`; card padding `22px` |
| Case chart | `margin-top:52px`; radius `8px` |
| Case brain map | `margin-top:48px`; padding `36px`; at `760px`, `24px`; radius `10px` |

Existing radii are component-specific: `2px`, `3px`, `4px`, `5px`, `6px`, `8px`, and `10px`. Match the component being extended.

### Responsive breakpoints

Do not replace the current breakpoints with framework defaults.

| File | Breakpoint | Existing behavior |
|---|---:|---|
| `index.html` | `max-width:560px` | Compact navigation, smaller logo/type, short `Consult` label |
| `index.html` | `max-width:820px` | Canvas opacity, one-column coach layout, reduced section/hero spacing, vertical timeline |
| `index.html` JavaScript | `W < 820` | Plexus switches from 170 particles to 95 and uses narrow placement |
| `case-hungarian-experiment.html` | `max-width:760px` | Brain-map stack, chart column compression, smaller section spacing |
| `case-hungarian-experiment.html` JavaScript | `W < 600` | Case brain switches from 150 particles to 110 |
| Both | `prefers-reduced-motion: reduce` | Suppress self-playing reveal/ambient motion according to each page's existing implementation |

There is no current `640px` CSS breakpoint. Add one only when an approved task explicitly requires it.

### Class and identifier naming

- CSS classes use lowercase semantic kebab-case: `.hero-content`, `.case-grid`, `.case-card`, `.coach-photo`, `.brand-strip-inner`.
- Short component namespaces are established and may be reused only inside their component: `.tl-*` for timeline, `.ds` for data-strip cells, `.bm-*` for brain-map elements, `.wt-*` for waveform labels.
- State/modifier classes are additive, not BEM double-hyphen names: `.rv.in`, `.tl-tag.result`, `.shift.up`, `.dot.exit.held`, `.delta.hold`.
- IDs are short lowercase identifiers used by links or JavaScript: `brainfx`, `problem`, `method`, `case`, `coach`, `book`, `rows`, `casebrain`, `casewave`.
- JavaScript uses function-scoped vanilla JavaScript and short camelCase/local names. Do not introduce a framework or module loader for isolated edits.
- Preserve established classes when cloning a pattern. Do not create a second visual vocabulary for a new case study.

### Reserved case-study insertion marker

The landing page's current reserved location is:

```html
<!-- FUTURE CASE CARD: Bocuse d'Or campaign. After competition + athlete approval,
     duplicate the <article class="case-card"> block above: tag "Inside a Bocuse d'Or campaign",
     the grid handles 2-3 cards automatically. -->
```

Treat this comment as a location marker, not as permission to begin the case study. When an approved task supplies newer instructions, the approved task controls the new card's content while the existing `.case-card` structure and styling remain the pattern.

### Dark-native rendering

The visual design is natively dark:

```css
html{background:var(--bg);color-scheme:dark}
body{background:var(--bg);color:var(--paper)}
```

Do not use `filter:invert(...)`, browser dark-mode inversion, duplicated light/dark themes, or a color-scheme workaround.

### Landing timeline

The desktop method timeline depends on CSS subgrid:

```css
.timeline{display:grid;grid-template-columns:repeat(3,1fr)}
.tl-stage{display:grid;grid-row:span 3;grid-template-rows:subgrid}
```

At `max-width:820px`, the existing CSS deliberately changes it into a vertical two-column timeline and sets `grid-template-rows:none`. Preserve both desktop and mobile implementations.

### Landing plexus canvas

The protected landing animation is `#brainfx` plus all related CSS and JavaScript.

Actual behavior:

- Deterministic seeded particle placement.
- 170 particles on desktop and 95 when `W < 820`.
- Spring return to each particle's home position.
- Ambient drift and node pulsing.
- Mouse-only pointer scatter on `pointermove`.
- Tap/click pulse on `pointerdown`.
- Return/refocus after pointer influence.
- `IntersectionObserver` and `visibilitychange` pause/resume behavior.
- Device pixel ratio capped at `2`.
- `prefers-reduced-motion` sets ambient drift to zero and node pulsing to a fixed radius while retaining spring physics, mouse scatter, tap pulses, and pointer event registration.
- Scroll-reveal motion is skipped entirely when reduced motion is requested.

Preserve this behavior exactly. “Reduced motion” does not mean disabling pointer-driven physics on the landing canvas.

### Case-study canvas

`case-hungarian-experiment.html` has a separate protected `#casebrain`/`#casewave` implementation. Under reduced motion it draws one static frame and does not register its pointer listeners. This differs from the landing canvas and must remain unchanged because the whole page is read-only.

## Copy rules

- Never alter Ben's wording in slogans, headlines, body copy, or client
  quotes. Reproduce verbatim. Capitalization changes are acceptable; word
  changes are not. If copy does not fit a layout, change the layout or ask.
- Client statements may appear inside quotation marks ONLY if sourced from
  a recorded, signed interview. Paraphrase from coaching notes must be
  attributed as paraphrase ("in his account", "he described") with no
  quotation marks.
- TrueFocus is coaching, not clinical practice. "Grounded in rehabilitation
  science" is acceptable. The word "clinical" is not.
- Lead with performance framing.
- Never claim coaching caused a competition result.
- Self-reported metrics must be labeled as self-reported. Never convert a
  subjective scale into a percentage change.
- Bocuse d'Or: factual statements only. No logos, no implied endorsement.
  Keep all claims client-centric.

For new or edited copy, these rules are controlling. Existing frozen copy is not permission to repeat language that conflicts with them.

## Do-not-touch list

- CNAME
- The plexus canvas animation
- The reduced-motion behaviour
- The CSS subgrid timeline
- Any booking or Calendly URL
- case-hungarian-experiment.html
- tfm-clinics (different repo)

Additional safeguards:

- Do not alter the embedded logo, coach portrait, or brand-strip image data unless a task explicitly thaws that asset.
- Do not reflow or re-encode a `data:` URL line.
- Do not change the existing Calendly destination:
  `https://calendly.com/ben-truefocusmind/30-minute-consult`
- Do not “clean up” nearby markup, copy, metadata, or styles while making a scoped change.
- Do not introduce analytics, SEO metadata, social-share assets, booking embeds, or integrations unless explicitly in scope.

## Known code-versus-recollection discrepancies

These observations are documentation, not authorization to fix anything:

1. The remembered type families are correct, but the body is `Newsreader`; `IBM Plex Mono` is used for labels/metadata and `Archivo` for display/CTA text.
2. The remembered “ink / cobalt” palette is not the current token vocabulary. There is no `--ink`; the source uses `--paper`, `--muted`, `--cobalt`, `--cobalt-deep`, and `--cobalt-text`.
3. The dark base `#0A0E17` is correct. The site is natively dark, but `index.html` declares `<meta name="color-scheme" content="only light">` while its CSS declares `color-scheme:dark`. The case page metadata and CSS both declare dark. Do not fix this contradiction without a metadata/theme task.
4. The landing plexus is not always approximately 170 particles: it is 170 on desktop and 95 below the JavaScript `820px` threshold.
5. The reduced-motion recollection is exact for `index.html`, but not for the separate Hungarian case-study canvas, which becomes static and non-interactive under reduced motion.
6. The desktop timeline uses subgrid with METHOD above and RESULT below. At `820px` and below it intentionally becomes a vertical timeline.
7. Most primary CTAs read `Request a consult`, but the compact navigation reads `Consult` and the final landing CTA reads `Request your consult`. Existing wording is frozen.
8. The booking URL is live, not a placeholder. The landing page nevertheless contains visible placeholder copy: `[ Booking embed goes here — Squarespace Scheduling / Calendly ]`.
9. The landing and case pages do not share identical measurements: their wrappers are `1080px` and `980px`, their body sizes/line heights differ, and the case page omits the `--slate` and `--line` aliases.
10. The extensible case grid does contain a reserved insertion comment for the Bocuse d'Or card. Use that exact location only after Task 2 is approved.

## Existing-copy provenance flags

Do not alter these while the relevant modules are frozen, but do not reuse or propagate them without verification:

- The frozen source uses the word `clinical` in the landing case card and Hungarian case report, despite the new copy rule prohibiting that word.
- The Hungarian page contains a quoted client statement. The repository does not document whether it came from a recorded, signed interview.
- The landing page states that `72% of client-defined goals are achieved`; the repository does not contain the supporting source or methodology.
- Existing claims and quotes remain frozen until an explicit copy/provenance task thaws them.

Raise these conflicts when a task would touch or reuse the affected copy. Do not silently resolve them.

## Definition of done

A task is done only when all applicable gates pass.

### Scope and diff gates

- The task named the thawed module before editing.
- A risk assessment was stated before overwrite or structural change.
- `git diff --name-only` contains only authorized files.
- Each authorized HTML diff is limited to the named component or insertion point.
- `git diff --check` passes.
- `git diff -- CNAME` is empty.
- `case-hungarian-experiment.html` is unchanged unless a future task explicitly supersedes its read-only status.
- No booking/Calendly URL changed.
- No package manifest, lockfile, build directory, framework, generated site, or deployment workflow was added.
- No formatter rewrote embedded asset lines.
- For governance changes, `AGENTS.md` and `CLAUDE.md` are byte-for-byte identical.

### Deterministic static checks

Run a local static server without adding dependencies to the repository, for example:

```bash
python -m http.server 8000
```

Then verify the exact changed pages in a real browser:

- No console errors attributable to the change.
- No missing assets or absolute local paths.
- No horizontal scroll at required widths.
- Native dark rendering; no inversion hack.
- Keyboard focus remains visible.
- Links resolve to the intended relative/root-relative destinations.
- Existing protected behavior remains intact.
- Reduced-motion behavior is tested separately from standard motion.
- For copy supplied by the owner, a deterministic text comparison against the source is zero-diff after HTML entity decoding and whitespace normalization. Do not “fix” a mismatch by changing the source wording.

For responsive visual tasks, capture or inspect at the task's required widths. Unless a task states otherwise, include at least a narrow mobile width and a wide desktop width.

### Human review gate

The second test is the owner's judgement on whether the change solves the problem. Do not merge, freeze the module again, or begin the next task until that review is complete.

### Publish and live gates

Before pushing a publishing change:

1. Re-run the GitHub Pages source query and confirm `main` plus `/`.
2. Confirm `CNAME` is unchanged.
3. Confirm the working branch is based on the intended current `main`.
4. Review the final diff and stated risk assessment.

After pushing:

1. Wait for the Pages deployment to complete.
2. Verify the changed page at `https://coach.truefocusmind.ca`.
3. Confirm the custom domain, HTTPS, layout, copy, links, and protected behavior on the live site.
4. Do not declare completion from a successful push alone.

## Current task boundary

Stop after Task 1. Present this `AGENTS.md` for owner review. Do not begin the Bocuse d'Or case study, create `case-bocuse-dor.html`, edit the case grid, or push a publishing change until the owner explicitly approves these rules.
