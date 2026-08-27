---
name: Alis-Design
description: Use when building, restyling, or reviewing any UI in Ali's projects — web pages, dashboards, extensions, portfolios, mobile screens, fixtures — or when output must feel premium and clean rather than templated or AI-generated. Also use before picking fonts, colors, spacing, chips/badges, buttons, or motion in any of Ali's front-end work.
---

# Ali's Design

## Overview

Ali's taste, distilled from shipped work (a browser extension, an internal management portal, a personal portfolio): **quiet warm surfaces, near-black ink, one weighted action, and motion that encodes real state.** The fastest way to fail is decoration that carries no information — Ali deletes it on sight ("green glowing dots that serve no purpose"). The fastest way to win is restraint executed precisely: exact spacing, exact alignment, one signature element.

**Violating the letter of this profile is producing the AI-slop Ali rejects.**

## Companion skills (load with this one)

- **emil-design-eng** — REQUIRED for any motion/interaction pass. Ali explicitly values its results ("looks nice and is also smooth").
- **apple-design** — REQUIRED when gestures, springs, materials/translucency, or reduced-motion behavior are in scope.
- **hallmark** or **frontend-design:frontend-design** — for greenfield pages/landing pages (structure + anti-slop gates). This profile overrides their palette/type picks wherever they conflict.
- **dataviz** — for any chart or dashboard tile.

## Open axes — ask before assuming

Most of this skill is locked taste and never worth a question. But a few axes are genuinely per-project choices, and silently defaulting on them takes away freedom Ali wants. When the brief doesn't pin them down, **ask once, in one compact batch (max 4 questions), before building** — via the environment's question tool when available, otherwise plain chat — with a recommended default marked on each. Never ask about locked taste, and never re-ask anything the brief or the project already answers.

| Axis | Ask when unspecified | Options (default first) |
|---|---|---|
| Display voice | any new surface, **especially portfolio/editorial and dark ones** | **Geist/sans throughout** · serif display (Playfair) for titles · Space Grotesk / Outfit display |
| Accent hue | new project, rebrand, or new theme | **temperature-matched canon pick** (warm light → editorial red · cool dark → violet/sky · warm dark → amber) · a brand hue, ink-mixed per the harmony rule |
| Theme scope | new site/app | **single warm light** · light + dark pair · full multi-theme set with picker |
| Signature element | any surface that earns one memorable moment | propose 1–2 candidates tied to the surface's real state and let the user pick |
| Motion register | marketing/editorial surfaces | **quiet product motion** · expressive (staggers, orchestrated reveals) |

If no user is reachable (subagent, CI, automation): take every default and **open the reply by stating the assumptions** — e.g. "Open axes defaulted: sans display, amber accent, single dark theme" — so they're visible and reversible. An unstated assumption on an open axis is a failure even when the default was right.

## Tokens — the palette Ali keeps choosing

Everything in OKLCH CSS custom properties. Never inline a raw color twice; derive states with `color-mix`.

| Role | Light (default) | Dark |
|---|---|---|
| Ground | warm bone `oklch(98% 0.004 85)` / cream `#f7f6f3` | near-black `#0D0D0D`–`oklch(19% 0.008 80)` |
| Surface | `#fffdfb` white-warm cards | `#151515`–`#15151c` |
| Ink | near-black `oklch(19% 0.008 80)` | `#F2F2F2` |
| Text-2 / Text-3 | `oklch(50%/68% 0.01 80)` | `#9A9A9A` / muted |
| Hairline | ink at 8–16% alpha | ink-inverse at 10–14% |
| Primary action | **black pill** (ink fill, ground-token label) | **soft paper pill** — the theme's warm ink token eased toward the ground, `color-mix(in oklab, var(--ink) 90-93%, var(--ground))`; never pure `#fff` (a stark white pill glows against a dark ground and reads as slop); label = ground token |
| Accent | ONE hue, used sparingly (focus rings, key links). Editorial red `#b44b3d`, amber, warm green suit warm light grounds; cool blues/violets (`#0ea5e9/#4f46e5/#8b5cf6`) belong on dark or cool grounds — never more than one accent per surface |

- **Accent harmony (hard rule):** the accent must share the page's temperature or it reads as pasted-in slop. On a warm ground, never drop in a stock cool hue raw — derive the usable value by inking it (`color-mix(in oklab, <hue> 75-85%, var(--ink))`) and cap chroma ≈ 0.12, or pick a warm-family hue instead. Squint test: the accent should look made of the same material as the ink. Text links on light grounds default to **ink + animated underline**; a colored link is the exception, not the default.
- Neutrals always carry a warm hue bias — never pure grey.
- Semantic red/green only for error/success, toned (`oklch(55% 0.16 30)`, `oklch(52% 0.10 155)`).

## Theming & recoloring

Any site may need multiple themes, or a wholesale recolor later. Both are cheap **only if every color flows through the token layer** — so that is a hard rule, not a preference:

- **Components never name a color.** Every `background`, `color`, `border-color`, `fill`, `box-shadow`, and `accent-color` references a token (`var(--ink)`, `var(--surface)`, …), and every hover/active/tint/hairline is **derived** via `color-mix` from those tokens. Then a theme (or a full rebrand) is one `:root` block — nothing else changes, and every derived state re-harmonizes automatically. A raw hex inside a component is a bug even if there's only one theme today.
- **A theme is a small named set** — ground, surface, accent, ink (text tiers and hairlines derive from ink) — applied via `:root[data-theme="name"]` and persisted in `localStorage` (`<app>:theme` key). Default: the warm light theme; honor `prefers-color-scheme` only when the user hasn't picked.
- **The accent-harmony rule applies per theme**: each theme's accent is chosen for *that* ground's temperature. Ali's shipped five-theme set is the canon:

  | Theme | Ground | Accent | Why it matches |
  |---|---|---|---|
  | cream (default) | `#f7f6f3` warm light | `#b44b3d` editorial red | warm on warm |
  | graphite | `#ffffff` neutral light | `#4f46e5` indigo | cool on neutral |
  | frost | `#fafbfc` cool light | `#0ea5e9` sky | cool on cool |
  | midnight | `#0a0a0f` cool dark | `#8b5cf6` violet | cool on cool dark |
  | obsidian | `#1a1612` warm dark | `#f59e0b` amber | warm on warm dark |

  Never carry one accent across all themes unchanged — pair accent to ground, per theme.
- **Semantic + identity colors adapt, not swap**: success/warn/error and peer colors keep their hue in every theme but adjust lightness/chroma per ground (lift L ~8–12% on dark grounds) so they stay recognizable *and* legible. Shadow/tint alphas are tokens too — dark themes need higher alphas.
- **Theme transitions are eased, never a hard flash.** Arm color transitions only during the switch (never on page load — that animates the initial paint), and prefer a View Transitions cross-fade where available. Instant under `prefers-reduced-motion`. Avoid abrupt dark↔light brightness jumps — ~250–350ms is right.

One example carrying the whole mechanism:

```html
<style>
  :root { /* cream (default) */
    --ground: #f7f6f3; --surface: #fffdfb; --ink: oklch(19% 0.008 80);
    --accent: #b44b3d;
    --text-2: color-mix(in oklab, var(--ink) 62%, var(--ground));
    --hairline: color-mix(in oklab, var(--ink) 12%, transparent);
    --pill-fill: color-mix(in oklab, var(--ink) 6%, var(--surface));
  }
  :root[data-theme="midnight"] {
    --ground: #0a0a0f; --surface: #15151c; --ink: #f2f2f4; --accent: #8b5cf6;
  }
  :root[data-theme="obsidian"] {
    --ground: #1a1612; --surface: #25201b; --ink: #f0ebe4; --accent: #f59e0b;
  }
  /* Derived tokens re-harmonize automatically — no per-theme component CSS. */
  :root.theming, :root.theming * {
    transition: background-color 300ms ease, color 300ms ease,
      border-color 300ms ease, fill 300ms ease;
  }
  @media (prefers-reduced-motion: reduce) {
    :root.theming, :root.theming * { transition: none; }
  }
</style>
<script>
  function setTheme(name) {
    const root = document.documentElement;
    root.classList.add("theming");                    // arm transitions for the switch only
    if (name === "cream") root.removeAttribute("data-theme");
    else root.dataset.theme = name;
    localStorage.setItem("app:theme", name);
    setTimeout(() => root.classList.remove("theming"), 350);
  }
  const saved = localStorage.getItem("app:theme");    // apply before paint, NO transition
  if (saved && saved !== "cream") document.documentElement.dataset.theme = saved;
</script>
```

Theme pickers render as swatch buttons (ground + accent dot per swatch, the ThemePicker pattern), with the active swatch ringed.

## Type

- **Geist first** for product UI: `"Geist", -apple-system, "SF Pro Text", "Segoe UI", system-ui, sans-serif`. Ali: "a good font is geist."
- Alternates already in Ali's systems: Outfit or Space Grotesk (display) + Inter (body). Mono: Geist Mono / JetBrains Mono — for code only, never for UI labels.
- Serif display (Playfair Display) is an **opt-in display voice** — chosen through the Open axes questions below, never assumed (not even for portfolios or dark editorial surfaces), and never for body text.
- Sentence case everywhere. **No uppercase mono micro-labels** — a confirmed Ali rejection.
- Weights 400/500/600; tracking −0.01 to −0.02em on titles, 0 on body.

## Shape & surfaces

- Radii: 12px fields, 14–16px cards/bars, **full pill for CTAs**. Soft squircle feel; never `rounded-lg`-everywhere sameness.
- Hairline borders over shadows. Shadows only for floating chrome: `0 1px 2px` + long soft `24–32px` at low alpha.
- Floating chrome (navbars, toolbars) = translucent glass: `backdrop-filter: blur(16-20px) saturate(1.4-1.6)`, warm-tinted semi-transparent bg, hairline border, sticky with a top gap.
- **Scroll-responsive nav** — the front-page pattern Ali shipped; the default for marketing/landing navs. At rest the nav floats inset over the hero (top gap, hairline border, 14–16px radius, soft shadow); once the page scrolls past a small threshold (~80px) it **eases into a full-width docked bar** — flush to the top edge, radius 0, bottom hairline only — and floats again when you scroll back up. Morph, don't swap: one element transitioning `top/width/border-radius/border-color/box-shadow` together over ~300ms strong ease-out, wrapped in a `position: sticky` shell of constant height so the morph never reflows the page. Scroll detection is a rAF-throttled `{ passive: true }` listener whose class write is a no-op unless the threshold was actually crossed. Glass in both states; the state change is instant under `prefers-reduced-motion`. (The inverse pairing — docked at rest, floating pill on scroll — is the same mechanic; pick per surface.)
- **Footers keep their content.** Restraint cuts decoration, not navigation: a product footer still carries the brand + a one-line description, sentence-case link columns (product / company / support), and a hairline-topped bottom row (© line + short note). Stripping it to a single link row reads unfinished, not clean. Column headings are 13px weight-600 sentence case — never uppercase tracked labels.
- **Two sanctioned chip patterns — pick by what the color means:**
  - **Identity chip** (peers, owners, categories — the color IS the identity): 1px border in the item's color + 7–12% tint fill + colored text. State change = border strengthens to 2px + tint deepens. Never solid-fill, never overlapping initials.
  - **Status pill** (workflow states — approved, in review, queued, on-track, at-risk, overdue): the management-portal look Ali shipped and screenshot-approved. **Soft warm-neutral fill, NO border** — fill ≈ `color-mix(in oklab, var(--ink) 6%, var(--surface))`, **ink text** at weight 500, 11px, ~20px height (17/24 sm/lg), tabular-nums, generous side padding (10–12px), full radius — with a **6px semantic dot** on the left carrying the only color (success `#4a6b28` · warn `#c0892a` · error `#b03a2f` family, or theme tokens), `aria-hidden` because the label text already states the status. Never a hairline-outlined transparent pill for statuses, never a saturated colored fill, never a bold colored label — the saturated version is the "straight slop" this spec replaced.
- Whole card is the click target; a small "Read →" alone is not affordance.

## Motion

- Easing: strong ease-out `cubic-bezier(0.16, 1, 0.3, 1)` (or `.23,1,.32,1`). Durations 140–200ms interactions, 300–450ms entrances/reveals. Springs: duration ~0.45s, bounce ≤ 0.15–0.2, Framer `layoutId` for shared-layout indicators (nav underlines).
- Press feedback: `transform: scale(0.97)` on `:active`, ~140ms.
- Link hover: `::after` underline `scaleX(0→1)` ~0.3s + color lift + `translateY(-1px)`, gated behind `@media (hover:hover) and (pointer:fine)`.
- Card/list affordance: arrow nudges `translate-x` 2px on hover, 150ms.
- Entrances once per mount (`@starting-style` fade + 6–8px rise); **never** transition elements rebuilt on every render tick.
- **The signature element**: exactly one memorable moment per surface, and it must encode true state (e.g. a CTA that visibly fills as a lobby readies). Decoration-only animation is cut.
- `prefers-reduced-motion` (and `prefers-contrast`, `prefers-reduced-transparency` for glass) handled in every stylesheet. Non-negotiable.

## Floating layers & controls

- **Tooltips are always custom** — the native `title` attribute is banned (Ali has replaced it in every project). The recipe Ali shipped twice: inverted-contrast bubble (**ink bg, paper text**), 11px/500, 6–8px radius, small soft shadow, positioned ~6px off the trigger with center/start/end alignment for viewport edges. Reveal: **~500ms delay on hover** (kills flicker), **instant on `:focus-visible`**, fade + scale from 0.97 over 125–150ms ease-out with `transform-origin` toward the trigger. Once one tooltip is open, adjacent ones open with no delay and no animation. A11y: tooltip `aria-hidden` with the trigger carrying `aria-label` (or `role="tooltip"`), and it must never be the only way to get the information. In stacks, hovering lifts the trigger (`z` raise) above neighbors.
- **Dropdowns/selects are custom, never native `<select>`** — the Select component Ali shipped in the portal and reuses everywhere (a native select's open menu is an unstylable platform widget). The recipe, exactly: the trigger is a field-styled `role="combobox"` **button** — field radius, hairline border, surface bg, 13–14px weight-500 ink text, left-aligned and truncating — whose border strengthens on hover/focus, with a small muted chevron (~9px, 1.4 stroke, `text-3` color) that **rotates 180° when open** over 150ms ease-out. The menu is a listbox popover **4px below the trigger at exactly the trigger's width**: surface bg, hairline border, radius matched to the field, **4px inner padding**, soft layered shadow, `max-height` ~260px with inner scroll. Entrance: fade + **`scale(0.95 → 1)` over ~180ms ease-out** with `transform-origin` at the trigger edge — starts at 0.95, never 0 (nothing real appears from nothing); no exit animation. Options are 13px weight-500 rows with 6–8px radius and muted (`text-2`) color; exactly **one moving highlight** — `mouseenter` and ArrowUp/Down drive the same one — gets a soft ink-tint fill + ink text, and the selected row carries a small **accent checkmark** on the right. Behavior: click toggles; ArrowDown/Enter/Space opens with the highlight on the selected option; Enter/Space picks; Escape and Tab close; outside `mousedown` closes. Inside scrollable overlays (dialog bodies), position the menu `fixed` from the trigger rect, flip upward when space below is tight, and close on any outside scroll — the native-select behavior. A11y: `aria-haspopup="listbox"`, `aria-expanded`, `role="listbox"`/`role="option"` with `aria-selected`, checkmark `aria-hidden`.
- **Sliders / draggable controls**: track = warm hairline-tint; filled portion = ink (accent only if the value is the accent's job); thumb = white pill with hairline + soft shadow. The thumb tracks the pointer **1:1 while dragging** (never eased toward the finger); press = `scale(0.97)`–grab feedback; on release, snapping/settling may use the strong ease-out or a spring with bounce ≤ 0.2 (momentum earns bounce). Value readout uses the tooltip bubble above the thumb, live while dragging. Keyboard: arrow steps with visible focus ring.
- **Checkboxes are drawn, not native** (screenshot-approved over the `accent-color`-tinted native widget). Structure: a clickable label wraps a **visually hidden native `<input type="checkbox">`** (`position: absolute; opacity: 0; width/height: 1px` — semantics and keyboard stay native) plus an `aria-hidden` drawn box and the text. The box: **18px, 5px radius**, hairline border, surface bg, `display: grid; place-items: center`, holding a ~10×8 stroked tick (path `M1 4.2 L3.6 6.8 L9 1.2`, stroke 1.6, round caps/joins) in the **ground color**. Checked (`input:checked + .box`): fill and border both go ink; the tick fades in and **scales 0.8 → 1** — every transition 140ms strong ease-out. `input:focus-visible + .box`: accent border + 3px soft focus ring. Single-line row labels sit muted (`text-2`) and lift to ink when checked; labels that carry a description line stay ink. Rows on a ~10px rhythm. Radios reuse the recipe with a full-round box, but keep the surface bg when checked — border goes ink and an **8px ink dot** scales in (`0.8 → 1`) instead of the tick; an ink dot needs the surface behind it, so radios never take the ink fill.
- **Switches/steppers/segmented controls**: the moving indicator is a shared-layout slide (Framer `layoutId` or transform), 140–200ms strong ease-out, `accent-color`/fills from ink. Same reduced-motion collapse as everything else: state changes remain, travel goes.

## Spacing & alignment

- Grouped rows: 8–11px rhythm; legend/heading to first item ≈ 8px. Ali notices a 4px miss in both directions — verify, don't eyeball.
- Two-column layouts: the left column's first heading top-aligns with the sibling card's box edge.
- Optical alignment beats mathematical (nudge icons 1–2px into visual center).

## Banned (verbatim Ali rejections)

Native `title` tooltips, native `<select>` dropdowns on polished surfaces (the open menu can't be styled — use the combobox + listbox recipe), standalone liveness dots and "LIVE" pills (a semantic dot *inside a labeled status pill* is the one sanctioned dot), uppercase mono labels, whole-surface state color washes, saturated colored badge fills, a raw cool accent on a warm ground, decorative mascots/planets/loaders/cursor-followers, overlapping avatar stacks, solid-fill chips, spinner-when-a-state-fill-exists, invented metrics, purple-gradient heroes, `transition: all`.

## Verify before claiming done

1. Screenshot at desktop **and** 375px; actually read them and fix what's off (chips clipped? flags cut? spacing drift?).
2. If the project has a states/fixture page, every interactive element shows default·hover·focus·active·disabled·error·success there.
3. Re-run the project's tests/build. Screenshots + green tests, then ship.

## Common mistakes

| Mistake | Fix |
|---|---|
| Cool grey neutrals | Warm the hue (85° bone / 80° ink bias) |
| Solid colored chips | Identity → outline + low tint; status → neutral pill + 6px semantic dot |
| Stock indigo/blue accent on a warm bone page | Ink-mix the hue (`color-mix` 75–85% toward ink) or switch to a warm-family accent; links default to ink + underline |
| Raw hex inside a component / one accent reused across themes | Route through tokens; pick each theme's accent for its own ground |
| Theme switch flashes or animates the initial page load | Arm color transitions only during the switch; instant under reduced-motion |
| Pure `#fff` button/pill on a dark theme | Mix the theme's warm ink token 90–93% toward the ground so the pill shares its temperature |
| Animating rebuilt DOM | Static render; animate only persistent nodes |
| Two accents + semantic colors everywhere | One accent; semantic only on actual states |
| Native `<select>` styled with a chevron overlay | The full combobox + listbox recipe: rotating chevron, 180ms scale-from-0.95 menu, one moving highlight, accent checkmark |
| Native checkbox tinted with `accent-color` | The drawn 18px box: hidden native input, ink fill, tick scaling 0.8 → 1, accent focus ring |
| Footer stripped to a single link row in the name of minimalism | Full footer: brand + one-liner, sentence-case link columns, hairline-topped © row |
| "Looks done" without mobile shot | 375px screenshot first |
| Assuming an open axis (e.g. Playfair on a portfolio, an accent hue, theme scope) | Ask the batch of open-axes questions first; if no user is reachable, take defaults and state them up front |
