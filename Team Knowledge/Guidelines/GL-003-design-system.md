# GL-003 - Design System

> **This Guideline is a general rule every creative agent reads on every relevant action.** Charta, Pixel, and any future visual specialist consume this file at the start of every task. Iris is the default author, but the values are the user's. Once filled, every creative agent reads from here for consistent style.

> **Empty is honest; placeholder is dangerous.** Until you've actually chosen a value, the placeholder stays. A populated-with-defaults design system silently sets choices you never made. If a section below is still showing `<your brand X>`, the agent reads that as "not yet pinned" and either routes to Iris first or works in flagged fallback mode.

> **Edits are Iris-only.** The user proposes; Iris authors. Charta and Pixel only ever read this file. The split keeps the schema coherent — multiple authors silently drift it.

> **Population status (2026-05-13):** §1 Identity is empty pending a guided session with Jesse. §2 Color, §3 Typography, §4 Spacing are populated — formalized from Charta's Kong AI Gateway re-render of 2026-05-12. §5 Imagery and §6 Voice are empty.

---

## 1. Identity

- **Brand name:** `<your brand name>`  *(canonical capitalization, spacing, punctuation locked here)*
- **Voice/tone descriptors:** `<adjective 1>`, `<adjective 2>`, `<adjective 3>`  *(three to five; aim for combinations that aren't generic — "professional" and "innovative" are useless; "calm but direct" is useful)*
- **Audience:** `<one sentence on who you're speaking to — a person, not a demographic>`

---

## 2. Color palette

> **Authored 2026-05-13 — env-tier tokens formalized from Charta's Kong AI Gateway renders.** Subsequent edits MUST preserve the env-tier semantic mappings: amber=Dev, teal=Test, crimson=Prod. These are load-bearing in DXC enterprise-platform deliverables.

### 2.1 Surface model

myPKA color tokens are authored in **two surface modes**: `light` (default — used for printed PDFs, slides on white, web docs, infographics on paper-toned canvas) and `dark` (used for technical diagrams, runtime/observability views, dense engineering schematics). Every semantic token has a matching pair in both modes. Charta and Pixel pick the surface mode per artifact and pull from that column.

The palette is **enterprise-neutral**: deep navy text, cool greys, desaturated foregrounds. The env-tier accents (amber/teal/crimson) are the *only* saturated colors. Treat them as semantic signals, not decoration.

### 2.2 Neutrals and structure

| Role | Token | Light value | Dark value | Intent |
|---|---|---|---|---|
| Canvas (page bg) | `--color-canvas` | `#F4F6F9` | `#0f1117` | Outermost page background. The "paper" or "screen" surface. |
| Surface 0 (cards) | `--color-surface-0` | `#FFFFFF` | `#1a1f2e` | Resting card / panel surface. |
| Surface 1 (raised) | `--color-surface-1` | `#EEF1F6` | `#242a3a` | Slight elevation — section bands, table headers. |
| Surface 2 (raised+) | `--color-surface-2` | — | `#2e3549` | Dark-mode only. Deeper-elevation panels inside surface-1 sections. |
| Surface 3 (high) | `--color-surface-3` | — | `#374161` | Dark-mode only. Highest-elevation chip/badge backgrounds. |
| Border | `--color-border` | `#D0D7E3` | `#3d4663` | Default 1px divider, card outline, table grid. |
| Border light | `--color-border-light` | — | `#4a5474` | Dark-mode only. Hairline on raised surfaces. |
| Text — heading | `--color-text-head` | `#0F1C2E` | `#e8ecf4` | H1/H2/H3, high-contrast labels. |
| Text — body | `--color-text-body` | `#2C3A4D` | `#e8ecf4` | Paragraph copy, default ink. |
| Text — muted | `--color-text-muted` | `#6B7A94` | `#8b95b3` | Captions, secondary metadata, "as-of" timestamps. |
| Text — disabled | `--color-text-disabled` | — | `#5a6380` | Dark-mode only. Footnotes, deferred-state labels. |

### 2.3 Environment-tier tokens (Dev / Test / Prod)

The DXC Kong AI Gateway program operates a Dev → Test → Prod promotion model. These tokens are the **canonical visual encoding** of that tri-environment shape across every myPKA deliverable. Color choice matters: amber for Dev signals "work-in-progress, not yet verified"; teal for Test signals "verified, pre-promotion"; crimson for Prod signals "regulated traffic, change with care."

Each tier has a **foreground** (text/icon color, also used for borders on light surfaces), a **background tint** (panel fill behind tier-scoped content), and a **strong border** (the saturated stroke used for env-scoped frames and accent dividers).

| Role | Token | Light value | Dark value | Intent |
|---|---|---|---|---|
| Dev — foreground | `--color-dev` | `#6B3A00` | `#d4860a` | Text and icon ink for Dev-environment elements. Light value is a deep amber; dark value is the saturated amber. |
| Dev — background | `--color-dev-bg` | `#FDF5E8` | `#201505` | Panel fill behind Dev-scoped content. Light = pale amber wash; dark = near-black with amber undertone. |
| Dev — border | `--color-dev-border` | `#D4860A` | `#8a5800` | Saturated stroke for Dev-env frames, dividers, env-tier dots. |
| Test — foreground | `--color-test` | `#1A5C6B` | `#2a8fa8` | Text and icon ink for Test-environment elements. Light = deep teal; dark = saturated cyan-teal. |
| Test — background | `--color-test-bg` | `#E4F3F8` | `#051820` | Panel fill behind Test-scoped content. |
| Test — border | `--color-test-border` | `#2A8FA8` | `#1a6880` | Saturated stroke for Test-env frames. |
| Prod — foreground | `--color-prod` | `#6B1A1A` | `#c83232` | Text and icon ink for Prod-environment elements. Light = deep crimson; dark = saturated crimson. |
| Prod — background | `--color-prod-bg` | `#FDE8E8` | `#200505` | Panel fill behind Prod-scoped content. |
| Prod — border | `--color-prod-border` | `#C83232` | `#8a1a1a` | Saturated stroke for Prod-env frames. |

**Accessibility — WCAG contrast verified 2026-05-13:**

- Light surface, all three tiers: foreground-on-tier-bg clears WCAG AAA (≥ 7:1) for normal text. Safe for body and labels.
- Dark surface, Dev (`#d4860a` on `#0f1117`): clears AAA (~7.1:1). Safe.
- Dark surface, Test (`#2a8fa8` on `#0f1117`): clears AA (~5.0:1). Safe for normal text; fine for large text and UI.
- Dark surface, Prod (`#c83232` on `#0f1117`): clears AA at ~4.9:1 — **borderline**. Safe for large text (≥18pt or 14pt bold), icons, and graphical strokes. For small body text on dark surfaces, prefer pairing crimson with a lighter weight surface (e.g., on `#1a1f2e`) or use the lighter ink `#e86060` (the variant Charta already uses for dp-name labels in the K8s render) which clears AAA on `#0f1117`. Do not place small (< 14px) crimson body text directly on `#0f1117` without verification.
- Light-surface border values (`#D4860A`, `#2A8FA8`, `#C83232`) on pale tier-bg tints clear WCAG 1.4.11 non-text contrast (≥ 3:1). Do not use these as text colors on the light canvas — they are stroke/icon tokens.

**Do not use for:**

- **Crimson is reserved for Prod-tier signaling.** Do not use as a generic error/alert color in non-environment contexts. If the brand later needs a `--color-error` token for form validation, system alerts, or destructive UI actions, author a distinct error token (likely a different red, e.g., `#B91C1C`) — never alias `--color-prod` to `--color-error`. The semantic load on crimson is "Prod environment"; overloading it to also mean "error" silently teaches viewers the wrong mental model.
- **Amber is reserved for Dev-tier signaling.** Do not use as a generic warning/caution color. If the brand later needs `--color-warning`, author a distinct token.
- **Teal is reserved for Test-tier signaling.** Do not use as a generic info/success color. If the brand later needs `--color-success` or `--color-info`, author distinct tokens.
- **Do not introduce per-deliverable env-tier hexes.** If a render needs an env-tier color and these tokens don't fit, route to Iris to extend GL-003 — don't fork the palette inline.

### 2.4 Accent and supporting palette (Charta-introduced, formalize-in-place)

Charta has consistently used a handful of additional saturated tokens in technical diagrams for non-env semantic roles (Konnect SaaS control plane, identity/provider, observability, plugin chain, etc.). Documented here so they aren't re-invented; they are **dark-surface-primary** because that's where these roles surface most often.

| Role | Token | Dark value | Light analog | Intent |
|---|---|---|---|---|
| Accent (link/CTA) | `--color-accent` | `#4a90d9` | `#1B4F8A` | Default link color, neutral CTA, "selected" state in diagrams. |
| Accent muted | `--color-accent-muted` | `#2a5282` | — | Backgrounds behind accent-colored elements on dark surfaces. |
| Konnect (SaaS CP) | `--color-konnect` | `#1abc9c` | `#1A6B5A` | Kong Konnect control-plane elements, "managed/SaaS" semantic. |
| Provider (identity / upstream) | `--color-provider` | `#6c63d4` | `#5C3A8A` | Identity providers, upstream AI providers, third-party-as-a-service. |
| Plugin (Kong DP plugin) | `--color-plugin` | `#2a6041` | `#3A6B4F` | Kong data-plane plugins. |
| Conditional / PII / sensitive | `--color-conditional` | `#7a3030` | — | Conditional/sensitive nodes (ai-sanitizer, PII redaction). Distinct from Prod-crimson by being de-saturated and dark-only. |
| Observability | `--color-obs` | `#4a6ea8` | — | Logging, telemetry, OTLP/syslog flow lines and panels. |

**Flag (open question for Tom):** the accent set above was introduced by Charta on a per-deliverable basis. The light-surface analogs are inferred from the org chart; they have not been pressure-tested across multiple light-surface renders. If a future deliverable needs accent/konnect/provider/etc. on a light surface, Iris should validate (or extend) before Charta improvises.

---

## 3. Typography

> **Authored 2026-05-13 — type stack formalized from Charta's Kong AI Gateway renders.** Heading and body roles converge on system-native sans for technical surfaces; Inter is the cross-platform fallback when system fonts are not predictable (web deliverables, shared HTML).

### 3.1 Font stack

| Role | Family | Weights | Usage |
|---|---|---|---|
| Heading | `'SF Pro Display', 'Segoe UI', system-ui, sans-serif` | 600, 700, 800 | H1, H2, section heads, hero display in technical diagrams. System-native preferred for crisp on-screen rendering. |
| Body | `'SF Pro Text', 'Segoe UI', system-ui, sans-serif` | 400, 500, 600 | Paragraph copy, captions, labels, body text in cards and panels. |
| Web fallback | `'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif` | 400, 500, 600, 700, 800 | Use Inter (Google Fonts preconnect) when the deliverable is a public-facing HTML artifact and font fidelity matters across reviewer machines. Charta's org chart uses this stack. |
| Mono | `'SF Mono', 'Cascadia Code', 'Consolas', monospace` | 400, 500, 700 | Code, config keys, env-scoped identifiers (`vault://<env>/...`), CLI references, tabular numerics, env-tier badge labels, diagram annotations. **Mono carries the technical voice of the brand — use it generously for any identifier-shaped token.** |

**Selection rule:** for internal / Apple-ecosystem-leaning artifacts, use the SF Pro stack. For artifacts that will render in browsers across mixed OS environments (DXC stakeholder review on Windows, mobile preview, public-facing slides), use the Inter stack. Don't mix Inter and SF Pro in the same deliverable.

### 3.2 Type scale

| Token | Size | Line-height | Use |
|---|---|---|---|
| `--text-display` | `26px` | 1.2 | Hero / cover-page titles in infographics and decks. |
| `--text-h1` | `20px` | 1.3 | Page titles, primary section headers. |
| `--text-h2` | `15px` | 1.35 | Section heads inside a page. |
| `--text-h3` | `12px` | 1.4 | Subsection heads, card titles. |
| `--text-body` | `11px` | 1.45 | Paragraph copy, default ink. |
| `--text-caption` | `10px` | 1.4 | Captions, metadata, "as-of" lines. |
| `--text-micro` | `8px` | 1.5 | Diagram annotations, flow-line labels, badge text. Use only inside dense technical diagrams; never in body prose. |

The scale is intentionally **dense** because Charta's primary surfaces are information-rich technical diagrams meant to be printed or zoom-rendered, not landing pages. If a future creative role surfaces a content-marketing deliverable (blog hero, social card), Iris will likely extend this scale upward — flag and route.

---

## 4. Spacing scale

> **Authored 2026-05-13 — spacing scale formalized from Charta's Kong AI Gateway renders.** 4px base with a six-token ladder. The org chart uses `--sp-*` naming; the K8s infographic uses `--space-*`. **Canonical token prefix is `--space-`** (matches the §6 Spacing field name and the rest of the design-system grammar). Charta should standardize on `--space-*` in future renders; existing `--sp-*` usage is grandfathered until next touch.

### 4.1 Base unit and ladder

- **Base unit:** `4px`  *(finer-grained control than 8px; matches Charta's dense technical-diagram surfaces where 4px gaps between adjacent nodes and badges are routine).*

| Token | Value | Multiple | Use |
|---|---|---|---|
| `--space-xs` | `4px` | × 1 | Hairline gaps; badge-to-label spacing; icon-to-text spacing inside chips. |
| `--space-sm` | `8px` | × 2 | Button padding (vertical); dense list items; inter-card gaps inside a flex row. |
| `--space-md` | `16px` | × 4 | Card padding; paragraph spacing; section-internal gaps. |
| `--space-lg` | `24px` | × 6 | Section spacing within a page; between cards in a grid. |
| `--space-xl` | `32px` | × 8 | Section spacing on hero / cover bands; outer page padding. |
| `--space-2xl` | `48px` | × 12 | Page-level rhythm; bottom padding on body; between major content blocks. |

### 4.2 Layout container

| Token | Value | Use |
|---|---|---|
| `--page-width` | `1320px` | Default fixed page width for Charta infographics (matches A4-landscape-ish print rendering and 1920px-screen review comfort). Override per deliverable when the surface requires (e.g., social cards). |
| `--radius` | `8px` | Default card / panel border-radius. |
| `--radius-sm` | `4px` | Small chips, badges, tag-style elements. |

---

## 5. Imagery style

- **Photography style:** `<editorial / candid / studio / lifestyle / none>`
  *Notes:* `<the look you're going for; link one example image if you have one>`
- **Illustration style:** `<line / painted / flat / 3D / mixed / none>`
  *Notes:* `<the look you're going for; link one example>`
- **Icon style:** `<line / filled / two-tone / hand-drawn / none>`
  *Family:* `<Lucide / Phosphor / Heroicons / Tabler / custom — pick one and lock>`

This section drives Pixel's prompt construction directly. The more concrete you are here, the more on-brand Pixel's outputs.

---

## 6. Voice samples

Three short example sentences in your intended voice. These are the canonical reference for any caption, headline, or body copy a creative agent writes.

1. `<your first voice sample sentence>`
2. `<your second voice sample sentence>`
3. `<your third voice sample sentence>`

*If you're stuck: write the first sentence of an email to your ideal customer. Then write a button label. Then write a one-line product tagline.*

---

## How agents use this file

- **At session start, every creative agent reads this Guideline.** Charta and Pixel always; Iris on every authoring or audit task.
- **If a section the task needs is empty (still showing `<placeholder>` values),** the agent does not improvise. Two paths:
  1. Route to Iris via [[SOP-009-author-a-design-system]] to populate.
  2. Work in flagged fallback mode (neutral-style for Pixel, no-style for Charta) and note in the deliverable: "GL-003 §X not populated; revisit when populated."
- **When this Guideline evolves,** in-flight deliverables that referenced the changed section are flagged for re-render. Older deliverables become stale candidates and get re-rendered next time they are touched (boy-scout rule), not bulk-rebuilt on the spot.
- **Audit cadence.** Iris runs [[SOP-010-audit-content-for-design-system-compliance]] when the user requests it, when a token is added, or when drift is suspected. The audit names violations; the user decides which to fix.

## References

- [[SOP-009-author-a-design-system]] — the procedure for populating or extending this Guideline.
- [[SOP-010-audit-content-for-design-system-compliance]] — the procedure for verifying deliverables against this Guideline.
- [[SOP-007-build-an-infographic]] — Charta's skill; reads from this Guideline.
- [[SOP-008-generate-a-styled-image]] — Pixel's skill; reads from this Guideline.
- [[GL-001-file-naming-conventions]] — slug, date, filename rules.
- [[GL-002-frontmatter-conventions]] — entity frontmatter schema.
- [[Team/Iris - Design System Architect/AGENTS]] — Iris's contract; the default author of this Guideline.
