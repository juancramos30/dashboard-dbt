# Design System: Del Bravo Trade & Specialties of Mexico — Operations Wallboard

## 1. Visual Theme & Atmosphere
A cockpit-dense, always-on logistics command-center display, built to live on an office monitor or TV and be read at a glance, never scrolled, never clicked. Density is high (9/10) — two business units' live order data must coexist on one screen with zero overflow. Variance is moderate (5/10): the two company zones (Del Bravo Trade in signal blue, Specialties of Mexico in deep navy) create deliberate asymmetry in color-coding, while the internal grid stays regular and predictable so operators can scan numbers instantly. Motion is restrained (3/10) — this is a passive, unattended display, so animation exists only to signal "this is alive" (a breathing live-indicator, a soft crossfade on data refresh), never to entertain or distract. The mood is clinical and calm under pressure — an air-traffic-control panel, not a marketing dashboard.

## 2. Color Palette & Roles
- **Night Ops** (#0B0E14) — Primary background, replaces the slightly blue-leaning #0d1117. A true off-black with a faint cool cast.
- **Slate Panel** (#12161F) — Panel, table-header, and card-fill surface.
- **Hairline** (rgba(148,163,184,0.14)) — 1px structural borders/dividers, replacing flat #30363d.
- **Fog White** (#E7EAF0) — Primary text, KPI totals, table data.
- **Steel Mist** (#8891A3) — Secondary text, labels, metadata, timestamps.
- **Signal Blue** (#3D7DFB) — Single UI accent. Used only for Del Bravo Trade's brand zone (panel header, section titles, focus rings). Desaturated from the prior #1D4ED8 to stay under 80% saturation and avoid the neon-AI look.
- **Harbor Navy** (#16335E) — Specialties of Mexico's brand-zone color. This is a second *entity-identity* color, not a UI accent — it exists to let operators tell the two companies apart at a glance, the same way two airline tail colors differ. It never competes with Signal Blue for emphasis (no buttons, no focus states, no links use it).
- **Verdant Signal** (#34A853) — Semantic "Enviado / shipped" state. Desaturated from #3fb950.
- **Alert Coral** (#E5534B) — Semantic "Pendiente / overdue" state. Desaturated from #f85149.
- **Amber Flag** (#D6A23C) — Semantic "En proceso / in progress" state. Desaturated from #d29922.

These three status colors are functional state indicators, not decorative accents — they're exempt from the one-accent rule the same way a traffic light's red/amber/green are exempt; they encode meaning, not brand.

## 3. Typography Rules
- **Display / section titles:** `Geist`, weight 700, tracking -0.02em on uppercase labels. Replaces the system-default `Calibri, Arial` stack, which has no character and renders inconsistently across OSes.
- **Body / table text:** `Geist`, weight 500–600 for data (this dashboard has no "casual" copy — everything is a number or a status, so body weight skews heavier than a marketing page).
- **Mono / numeric data:** `Geist Mono` with `font-variant-numeric: tabular-nums` for every KPI value, day-count, and percentage. Density exceeds the 7/10 threshold that mandates monospace numerals — without it, columns of digits drift and become harder to scan.
- **Banned:** Calibri, Arial, system-ui fallbacks as primary; Inter; any serif (dashboards never use serif).

## 4. Hero / Topbar
There is no marketing hero here — the topbar is a utility instrument strip, not a hero section, so the inline-image-typography technique doesn't apply. Instead:
- Title left-aligned (not centered — centering a dashboard headline wastes the strongest reading position on a static label).
- A small breathing "live" dot (Verdant Signal, 2s opacity pulse 0.4↔1) sits before the "Actualizado" timestamp — the dashboard's one perpetual micro-interaction, signaling the feed is live without being decorative.
- Right-aligned: last-updated timestamp + per-company order counts, in Geist Mono.
- No CTA of any kind — this view is read-only.

## 5. Component Stylings
- **Panels (DBT / SoM):** No drop-shadow card look. Each panel is Slate Panel fill with a 3px top border in its entity color (Signal Blue or Harbor Navy) instead of a heavy shadow — at this density, a border-top divider communicates hierarchy better than elevation.
- **KPI strip:** Numbers in Geist Mono tabular figures, 32px, color-coded by semantic state. Hairline dividers between cells (kept from the current design — it works).
- **Tables:** Zebra striping kept (it materially helps row-scanning at this row count). Status badges stay as small rounded-rect pills (6px radius, consistent across the whole UI — currently mixed between 4px and 6px). One badge type gets motion: a row whose "días" is negative (late) gets a slow 3s opacity pulse on just the day-count text, signaling urgency without looping color or glow.
- **Donut charts:** Same Chart.js doughnuts, recolored to the desaturated semantic palette, with the total order count rendered in Fog White at the center of the cutout (currently empty) so the chart carries a number, not just a shape.
- **Loading state:** Replace the plain "Cargando..." text with a skeleton shimmer matching the exact panel/table grid dimensions, so the layout doesn't jump when real data arrives.
- **Error state:** Keep the existing inline red error text (no `window.alert`, already correct) — just restyle it in Alert Coral on Slate Panel instead of raw red-on-black.
- **Refresh behavior:** Replace the current hard `location.reload()` every 5 minutes with an in-place re-fetch + 200ms opacity crossfade on just the data regions — a full-page reload on an always-on wallboard causes a visible flash every 5 minutes that this redesign removes.

## 6. Layout Principles
- Keep the no-scroll, single-viewport "cockpit" grid (`grid-template-rows: auto 1fr auto auto auto`) — this is correct for a wallboard and should not become a scrolling page.
- Change `height: 100vh` to `min-height: 100dvh` defensively, even though this is primarily a desktop/TV display, in case anyone opens it on a phone to check status remotely.
- The two-column DBT/SoM split is intentional brand-pairing, not the banned "3 equal cards" pattern — keep it as CSS Grid `1fr 1fr`.
- No max-width container — this view is meant to fill an edge-to-edge kiosk screen, which is the documented exception to the usual 1400px-contained-content rule.

## 7. Responsive Rules
This is designed first for a fixed kiosk/TV viewport, but must not break if opened on a laptop or phone:
- Below 768px: the `panels`, `clients-row`, and `chart-section` grids collapse from `1fr 1fr` to a single column, and `overflow: hidden` is lifted in favor of normal scrolling — the cockpit-no-scroll constraint is a desktop/TV-only decision.
- Headline and KPI sizes scale via `clamp()` rather than fixed px so the topbar title doesn't wrap awkwardly on narrow screens.
- Touch targets aren't a primary concern (no interactive controls exist), but any future control must hit the 44px minimum.

## 8. Motion & Interaction
- Spring-physics easing (`cubic-bezier(0.32, 0.72, 0, 1)`) for the one-time entrance of table rows on first load (translateY 8px + opacity, staggered 30ms per row) — purely an arrival cue, never repeating.
- The live-dot pulse and the late-order text pulse are the *only* perpetual loops — everything else on this screen is static once rendered, by design, because constant motion on an always-visible wallboard becomes visual noise that operators tune out or find fatiguing over an 8-hour shift.
- All motion uses `transform` and `opacity` exclusively — no animated `top/left/width/height`.

## 9. Anti-Patterns (Banned)
- No `Calibri`/`Arial`/`Inter` — use Geist + Geist Mono.
- No pure `#000000` — use Night Ops (#0B0E14).
- No neon glow or saturated accent — Signal Blue and all semantic colors are desaturated.
- No emojis anywhere in labels or status text.
- No decorative looping animation beyond the two functional pulses defined above.
- No `location.reload()`-driven full-page flash as the refresh mechanism.
- No mixed border-radius values — standardize on 6px for badges/inputs, 8px for panels.
- No AI copywriting clichés in any UI copy (none currently present — keep it that way).
