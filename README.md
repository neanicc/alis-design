# Ali's Design

A personal design-taste skill for [Claude Code](https://claude.com/claude-code). Drop it in `~/.claude/skills/` and any agent building UI for you inherits a specific, tested aesthetic instead of the defaults every model reaches for: warm bone surfaces, near-black ink, Geist, one weighted action per screen, and motion that only exists when it carries information.

It was distilled from real shipped projects (a Chrome extension, a management portal, a portfolio) by mining session memory for every design decision I'd actually approved or rejected, then compressed into rules an agent can follow cold. The test protocol is strict: a fresh agent with zero conversation context reads only the skill and builds a component. If the output needs correction, the correction becomes a rule and the test reruns until it doesn't.

## Before and after

Both screenshots come from capable agents. The left one had no skill and produced the look I rejected as "robotic and AI-generated": mono uppercase labels, colored state washes, a glowing status dot that carried no information. The right one read `SKILL.md` and nothing else.

| Without the skill | With the skill (cold build) |
|---|---|
| ![Before](assets/before-without-skill.png) | ![After](assets/after-dashboard.png) |

More cold builds, none of them touched up afterward:

| | |
|---|---|
| ![Portfolio](assets/after-portfolio.png) | ![Modal](assets/after-modal.png) |
| ![Controls](assets/after-controls.png) | ![Mobile](assets/after-dashboard-mobile.png) |

One card across three themes with zero per-theme component CSS; only the token blocks change, and each theme's accent is paired to its own ground temperature:

| Cream | Midnight | Obsidian |
|---|---|---|
| ![Cream](assets/after-theme-cream.png) | ![Midnight](assets/after-theme-midnight.png) | ![Obsidian](assets/after-theme-obsidian.png) |

## What the skill locks down

The concrete stuff lives in [SKILL.md](SKILL.md); the short version is a warm OKLCH token system where every state derives via `color-mix`, a Geist-first type stack in sentence case, hairlines over shadows, two sanctioned chip patterns (colored outline for identity, neutral fill with a 6px semantic dot for status), custom tooltips because native `title` is banned, sliders that track the pointer 1:1, and an easing system built around `cubic-bezier(0.16, 1, 0.3, 1)` with reduced-motion handled everywhere. There's also a banned list of verbatim rejections. Standalone status dots. Saturated badge fills. A cool accent dropped raw onto a warm page. `transition: all`.

## What the skill asks about

Some axes are taste; others are per-project freedom, and an agent guessing on those is how you end up with a serif font you never asked for. So the skill splits them. Locked taste never gets questioned. Five open axes (display voice, accent hue, theme scope, signature element, motion register) trigger one batch of questions with recommended defaults before any CSS gets written. When no human is reachable, the agent takes the defaults and must open its reply by declaring them, because a silent assumption on an open axis counts as a failure even when the default was right.

## Install

```bash
mkdir -p ~/.claude/skills/alis-design
curl -fsSL https://raw.githubusercontent.com/neanicc/alis-design/main/SKILL.md \
  -o ~/.claude/skills/alis-design/SKILL.md
```

Or clone and copy. Reload skills (or restart Claude Code) and it auto-triggers on UI work; `/alis-design` invokes it directly.

## Prerequisites: companion skills

`alis-design` works on its own, but it names four companion skills and expects them for full effect, especially the motion passes. Install these into `~/.claude/skills/` (or the plugin marketplace where noted) before relying on it:

- **emil-design-eng**, built on [Emil Kowalski](https://emilkowal.ski/)'s design engineering philosophy from [animations.dev](https://animations.dev/). This is where the press-scale, tooltip-timing, and "should this animate at all" discipline comes from, and the skill requires it for any motion or interaction pass.
- **apple-design**, distilled from Apple's WWDC design talks (chiefly *Designing Fluid Interfaces*, 2018) into web terms. Required whenever gestures, springs, translucent materials, or reduced-motion behavior are in scope.
- **hallmark** (powered by Together AI), an anti-slop page-structure skill used for greenfield pages and landing pages. Where its palette or type picks conflict with this skill, this skill wins.
- **frontend-design** and **dataviz** from Anthropic's official Claude Code plugins, used for fresh visual direction and any chart work respectively.

Credit where it's due: this skill stands on those. It contributes the part they can't know, which is what one specific person keeps choosing.

## Samples

The five demo files in [`samples/`](samples/) are the actual cold-build outputs from testing, kept unedited: a judging dashboard, a dark portfolio section, a settings modal, a tooltip/slider/segmented-control card, and the four-theme switcher. Each is a single self-contained HTML file; open it in a browser. The themes one persists your pick in localStorage and eases the switch only during the change, never on load.

## How it got its rules

Seven correction rounds so far, each one anchored to a real piece of feedback and verified by rerunning the same brief cold:

| Round | Correction | Rule that came out of it |
|---|---|---|
| 1 | Baseline profile mined from memory across four projects | The token system, type, motion, spacing, banned list |
| 2 | "The blue does not match at all with the rest of the page" | Accent harmony: accents share the ground's temperature or get ink-mixed |
| 3 | A status pill from the management portal, screenshot as spec | Neutral pill + 6px semantic dot for workflow states |
| 4 | "This is what I see rn. Is this correct or did u copy it wrong?" | Screenshot beats documentation: pills are borderless soft fill, not hairline outline |
| 5 | Tooltips and sliders weren't covered | Floating layers and controls section; native `title` banned |
| 6 | Multi-theme sites should re-harmonize, not hardcode | Theming section with per-theme accents and eased switching |
| 7 | "It shouldn't assume Playfair for the title" | Open axes: ask before assuming, declare defaults when you can't ask |

The white-button round hides inside 6: on dark themes the primary pill is the ink token mixed 90 to 93 percent toward the ground, never pure `#fff`, because a stark white pill glows against a dark surface and reads as pasted-in.
