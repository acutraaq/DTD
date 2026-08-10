# Jalinan Merdeka — Concept Intro Screen

## Purpose
Standalone intro screen shown before `dtd-jalinan-merdeka-pin-unlock.html`, explaining the "Jalinan Merdeka" theme (3 pillars) to the townhall audience, in the same neon-tech visual style, before the audience proceeds into the interactive PIN-unlock quiz.

## Scope
New file: `dtd-jalinan-merdeka-intro.html`, sibling to the existing pin-unlock file. No changes to pin-unlock.html itself except that this new file links into it via a CTA button. No backend, no build step — static single HTML file, same as existing pattern.

## Visual style (reused from pin-unlock.html)
- Same fixed 1920×1080 `#stage`, scaled to fit viewport via `fitStage()`.
- Same `.frame` border/corner-flare treatment, same `#circuitSvg` animated circuit-weave background (script copied verbatim from pin-unlock.html — self-contained, no shared JS file).
- Same background image `assets/glitch-bg.png`.
- Same fonts: Caveat, Archivo Black, Space Mono (same Google Fonts `<link>`).
- Same CSS custom properties: `--navy`, `--gold`, `--red`, `--cyan`, `--panel`, `--paper`, `--green`.

## Content (condensed copy, meaning preserved from source material)
Three pillars, rendered in sequence:

1. **JALINAN — Threads of Connection**
   Cloud, AI, automation woven into one digital ecosystem — connecting every department, seamlessly.
2. **MERDEKA — Digital Liberation**
   Freedom from manual work, legacy bottlenecks, red tape. AI and automation liberate our people for higher-value work.
3. **KESEJAHTERAAN DINIKMATI — Well-being Enjoyed**
   Woven systems + liberated work = smooth operations, faster service, lasting peace of mind for all.

## Components
- **Status line** (top, same style as pin-unlock's `.status-line`): "Status: Initializing Jalinan Merdeka..."
- **Boot-log panel** (reuses `.quiz-card`-style frame/border/glow): renders pillar blocks sequentially. Each block: `> JALINAN` line (gold, bold, Archivo Black) followed by its description line (panel color, Space Mono), fading/typing in, with a pause before the next block starts. Data-driven from a static `PILLARS` array (mirrors the existing `QUESTIONS` array pattern in pin-unlock.html for consistency).
- **CTA button**: "BEGIN VERIFICATION →" — fades/pops in after the 3rd block finishes. Styled like `.answer-btn` but larger, gold border + glow (parallels the gold `.slot.filled` treatment). Click navigates to `dtd-jalinan-merdeka-pin-unlock.html`.

## Data flow
Fully static. No network calls, no backend. A `setTimeout` chain drives the sequential reveal, mirroring the timing/sequencing style already used in pin-unlock.html's `handleAnswer` flow.

## Accessibility / reduced motion
`@media (prefers-reduced-motion: reduce)` disables stagger/typewriter animation and shows all 3 pillar blocks plus the CTA button immediately (same guard pattern already present in pin-unlock.html).

## Testing
No automated tests — static page. Manual verification in browser:
- Reveal timing/order plays correctly once.
- CTA button appears only after all 3 pillars shown, and link navigates correctly to pin-unlock.html.
- `prefers-reduced-motion: reduce` shows everything instantly, no animation.
- Stage scales correctly to fit different viewport sizes (reuses existing `fitStage()`).

## Out of scope
- No changes to pin-unlock.html.
- No skip/fast-forward interaction (not requested — YAGNI).
- No multi-page site restructuring (explicitly deferred per user's earlier answer — this is a two-screen flow, not a full site rebuild).
