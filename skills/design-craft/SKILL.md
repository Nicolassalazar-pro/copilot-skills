---
name: design-craft
description: One consolidated interface-design reference for building great UI in ANY setting — desktop, mobile, e-ink (reMarkable), embedded/HTTP, TUI/CLI, and slide decks. Synthesises Apple's Human Interface Guidelines, cross-domain/non-tech UX (Rams, Norman, luxury/automotive HMI, pro-tools, radial/marking menus, print & wayfinding, game UX), and open-source desktop philosophy (GNOME/KDE/elementary/Material/Fluent). Built around the goal "intuitive and easy yet versatile and expandable — ease of use WITH full control" via the low-floor/high-ceiling/wide-walls model and an explicit ease-vs-power decision framework for how much control to expose. Use when designing or reviewing any UI: choosing layout/tokens/type/spacing/color, motion & latency budgets, gestures & radial menus, command palettes & progressive disclosure, accessibility, destructive-action safety, premium/quiet-luxury feel, and per-setting adaptation (esp. e-ink/embedded). Supersedes the web-design-dna skill (which it folds in, incl. the L1→L4 maturity ladder, DNA tokens, content-density caps, hero-motif library, and embedded/live-control/PWA-on-LAN patterns).
---

# Design Craft — One Skill for Great UI in Any Setting

*The consolidated interface-design reference. Supersedes `web-design-dna`. Synthesises Apple's HIG, cross-domain/non-tech UX (Rams, Norman, luxury/automotive, pro-tools, radial menus, print/wayfinding, games), and open-source desktop philosophy (GNOME/KDE/elementary/Material/Fluent) into one system — built around the goal: **intuitive and easy, yet versatile and expandable; ease of use WITH full control.***

Use this for any UI: desktop, mobile, **e-ink (reMarkable)**, embedded/HTTP, TUI/CLI, and slide decks. Full source research: `…/files/research/{apple-hig,cross-domain-ux,desktop-philosophy}.raw.md`.

---

## 0. The North Star — "Low floor, high ceiling, wide walls"

(Papert → Resnick, *Lifelong Kindergarten*, MIT Press 2017.) Every design decision is judged against three axes at once:

- **Low floor** — the first interaction succeeds with zero setup. A pen draws. A canvas accepts. The default is the answer for ~80% of users (Pareto + universal design).
- **High ceiling** — experts reach full power without leaving the product: shortcuts, command palette, modifiers, density, customization.
- **Wide walls** — the same primitives serve many use-cases; the product expands without redesign.

**This replaces the false "simple vs powerful" tradeoff.** You don't choose; you layer. The whole craft below is *how* to serve all three at once.

### The ease-vs-power decision framework ("how much control to expose")

For any feature/option, ask in order:
1. **Can a great default remove the choice entirely?** (GNOME/elementary, Pennington 2002). If yes — ship the default, no knob. *Every preference has a cost: QA, docs, support, pixels, and one more decision for the user.*
2. **If not, is it a workflow choice or a feature on/off?** Settings that merely enable/disable a feature are a design smell (KDE HIG) — fix the feature instead.
3. **If it's real, expose it by tier, not all at once (progressive disclosure):**
   `visible default → "Show more"/inline reveal → Preferences → Advanced/expert (palette, config, modifiers)`.
4. **Route complexity away from the per-interaction path** (Tesler's Law / Conservation of Complexity): push it to one-time setup, the system, or an expert opt-in — never make every user pay every time.

**Posture by product type:** consumer/onboarding → GNOME/elementary posture (opinionated, few knobs). Pro/power tool → KDE posture ("simple by default, powerful when needed" — visible simple path + deep optional control). Most things → default-simple **with** a high-ceiling escape hatch (command palette is the cheapest one).

---

## 1. Core philosophy (the editorial filter)

- **Clarity · Deference · Depth** (Apple). Clarity = legible & precise. Deference = chrome gets out of the content's way. Depth = hierarchy conveyed by layering/weight/motion. *On e-ink: get depth from type-weight + spacing, never shadow/translucency.*
- **Less, but better** (Rams #10). Every element justifies its cost in attention; if it can't, delete it.
- **Content is the interface — direct manipulation first** (Apple). Act on the visible object (tap/drag/pinch) with 1:1 tracking and immediate, reversible result. Menus/dialogs are fallbacks for ambiguity.
- **Recognition over recall.** Show options, recents, last-used. Don't make people remember.
- **Signifiers, not just affordances** (Norman 2013). On flat/e-ink UIs a hidden interaction *does not exist* — every action needs a visible cue.
- **Cohesion of character.** One motion vocabulary, one spring, one sound/haptic, one voice across the whole product — it should feel like a single personality.

---

## 2. The L1→L4 maturity ladder (self-grade, then fix the next thing)

| Level | Looks like | Move up by |
|---|---|---|
| **L1** | Template-on-rails: stock photo hero, generic "Get Started", feature-restating copy, flat buttons | Replace stock imagery with the real product UI; establish hierarchy |
| **L2** | Real but bland: real screenshot, one primary CTA, trust row, still literal copy | Zoom into the parts that matter; rewrite copy outcome-led |
| **L3** | Product-designer territory: centered hero + dual CTA, badge, **zoomed UI regions w/ callouts**, **bento grid**, real data in mocks | Add craft: hover/blur, smooth transitions, multi-select detail; relentless polish |
| **L4** | "Just well-crafted UI": specific modals not whole-app, outcome-led copy, animation craft, every grid cell purposeful. **No 3D, no custom illustration — just polish.** | Maintain with discipline |

> The trap is reaching for fancier graphics. The actual move is **craft applied to basics.**

---

## 3. Design tokens (consistency engine)

**Hold CONSTANT across a project (the "chrome"):** header/nav, footer, type family, spacing scale, radius scale, button shape, input shape (matches buttons), focus ring.

**VARY per surface (the "DNA"):** layout family (editorial/bento/asymmetric/centered/split), hero motif (see §13), accent pair (one bold + one muted — never two bolds), type-scale flavor, form-language (rounded-soft / rounded-tight / square).

**Token rules (from Material/Fluent/W3C-DTCG + desktop research):**
- **Semantic, never literal.** `color.role.primary`, `.label`, `.surface`, `.accent` — never hard-coded hex in components. This is what makes dark-mode / high-contrast / brand-swap / e-ink-grayscale all ride one layer.
- **Tonal color**, not swatches: define *tones per hue*; dark mode + high-contrast come for free (M3).
- **State colors (success/warn/error/info) live OUTSIDE the brand palette** — they're functional.
- **Never convey state by color alone** — pair with shape/icon/text (color-blind, screenshots, e-ink). Non-negotiable.
- **3-step surface scale**, not binary: `backdrop → surface → surface-alt`, each 5–8% apart.
- Keep a `dna_manifest.json` listing layout/hero/accent per page so the next page consciously differs.

---

## 4. Type, spacing, grid

- **Type roles, not pixel sizes:** `display / title / body / label / caption`. Reflow to the largest accessibility size (Dynamic Type). One family, 2–3 weights (400/500/600/700), 6–10 sizes.
- **Hierarchy via weight + size, never borders.**
- **8-pt spacing grid** (4-pt half-step for icon padding only): `4 8 12 16 24 32 48 64`. On e-ink bias larger; on embedded bias to physical-button rhythm.
- Body ≥ 14pt desktop / 16pt mobile. Line-height: body 1.5, headings 1.1–1.25, caption 1.4. Negative letter-spacing only on big display (-0.02 to -0.04em at 48pt+).
- **Whitespace > borders for grouping** (Gestalt proximity — free).

---

## 5. Content density & copy

**Density caps (lint them in CI; ship violations as bugs):** headline ≤ 8 words/60 chars · subhead ≤ 18 words · bullets ≤ 4/group · cards ≤ 6/grid · type-scale steps 3–4 · color tokens ≤ 5/surface · top-nav 4–6 · CTAs above fold = 1 primary + 1 ghost · trust logos 6–8.

**Copy:**
- **Outcome-led, not feature-led.** If the headline could sit in a feature-comparison table, it's L1/L2. If it could be a user's testimonial punchline, it's L4. ("Know where every click goes" > "Track your links".)
- **Named, imperative action buttons** — never "OK". The user reads the verb: "Save", "Delete", "Use template".
- **Positive phrasing** for options/toggles (avoid double-negatives).
- **Plain-language errors:** *what happened · why · what to do now.* Never cryptic codes.

---

## 6. Motion

- **Animation = communication, not decoration.** Only three legitimate purposes: (a) spatial continuity, (b) hierarchy/state change, (c) hinting at a gesture. If it does none, delete it.
- **Symmetric paths:** things return whence they came (dissolve-in/dissolve-out, never dissolve-in/slide-out).
- **Springs over timing curves** (Apple): parameterize as *damping + response*; default 100% damping (no overshoot); add bounce **only** when the driving gesture had momentum; snap with *projection* (`v × decel`), not nearest endpoint; rubber-band at boundaries.
- **Durations** 180–240ms; entrance `cubic-bezier(.2,.8,.2,1)`, exit `(.4,0,1,1)`. Animate only opacity/transform/color — never width/height/top/left (layout jank). One motion at a time.
- **Respect `prefers-reduced-motion` unconditionally** — substitute a cross-fade, never silence the feedback.
- **E-ink / embedded / low-refresh: SNAP, don't animate.** Motion that ghosts is harmful. Use instant state changes + a subtle text/icon mark; reserve one deliberate full-refresh flash for true repaints (page turn, zoom reflow, explicit "refresh").

---

## 7. Latency (the master variable)

Perceived speed beats actual speed; latency "shatters the illusion of an extension of you" (Apple).
- **< 100 ms** = first visible response to any tap/key (Doherty/Nielsen "instantaneous").
- **< 400 ms** = command result before flow breaks (Doherty Threshold).
- **< 1 s** = uninterrupted; **> 1 s** needs a determinate progress signal; **> 10 s** needs an exit.
- On **e-ink** the "first response" is the next refresh — design the refresh as the feedback, explicitly.

---

## 8. Interaction & gestures

- **Touch targets ≥ 44×44 pt with ≥ 8 pt gap** (pointer ≥ 28 pt); extend invisible hit area when visuals must be small. **Confirm on touch-up; drag-off cancels; touch-down only highlights.**
- **Every gesture has a visible button equivalent**, and the button's animation *teaches* the gesture (matching direction). Gestures are an accelerator, never the only path (Apple's hardest problem → always provide the fallback + a grabber/affordance).
- **Quasimodes (hold-to-modify) over toggle modes** (Raskin) — a held key reminds itself; a toggled mode is the #1 expert-error source.
- **The mode you're in is always visible** (vim/tmux status, selection-mode toolbars). Hidden modes are usability traps.
- **Zones don't collide** (esp. touch + stylus): reserve edges for system chrome, multi-finger for global actions, single-finger-on-content for writing/selection so a gesture never eats a stroke. On rM2 a plain `MouseArea` is finger-only (Wacom passes through) — use it for finger UI over the canvas.

### Radial / marking menus (for the hex tool menu and any frequent-tool surface)
- **Direction beats distance** for high-frequency selection (Fitts + Kurtenbach/Hopkins). Radial = muscle memory.
- **6–8 items per ring max; nest deeper for more.** Hex (6) is the sweet spot for hand+stylus.
- **Mark-ahead must work**: experts flick the path without waiting for the menu to draw; the drawn menu is for novices only.
- Center = current node / step-back. Breadcrumb the drill path.

---

## 9. The power layer (high ceiling, without clutter)

- **Command palette** (`Cmd/Ctrl-K` / `Ctrl-Shift-P`) in any app with >15 actions — the cheapest high-ceiling widget: every action self-documents via fuzzy search; novices discover, experts fly. (Embedded equivalent: a known list of named actions on one button.)
- **Four entry routes per important action:** shortcut · menu · palette · contextual gesture. Redundancy = accessibility for skill diversity. *But never make an important action shortcut-only* (visibility first).
- **Modifier keys / gesture multipliers** for amplified variants (⌥/⇧/⌘).
- **Spatial constancy on power surfaces:** never auto-reorder/auto-hide/"smartly" rearrange toolbars (Photoshop/Logic/Bloomberg). Muscle memory is sacred.
- **Settings = workflow choices, grouped by user task** (Sync/Privacy/Editing), **most-used first**, not by code module.

---

## 10. Safety & state

- **Undo over confirmation dialogs** (elementary/GNOME): confirmations train habituated clicking; undo respects time. Soft-delete + a timed Undo toast; e-ink → long-press + toast; embedded → countdown bar before commit. Hard-delete needs an explicit consequence-stating confirm.
- **High-consequence controls get unique shape, color, AND position** (aircraft-cockpit shape-coding) — Delete/Publish/Factory-reset must never look like their neighbors.
- **State survives kill→restart** (open doc, scroll pos, undo history). Closing/reopening should be indistinguishable from hide/reveal.

---

## 11. Accessibility (a design driver, not a checklist)

Baseline non-negotiable: contrast ≥ 4.5:1 (body ≥ 7:1 ideal), targets ≥ 44px, keyboard-reachable, screen-reader labels, respect reduce-motion + large-text, never color-alone. Design *with* VoiceOver / Differentiate-Without-Color / Increase-Contrast on — it makes the product better for everyone. Inclusive language (close not kill, exit not abort, worker not slave).

---

## 12. Premium / quiet-luxury

- **Premium = consistency of response, not richness of motion** (the Lexus-door principle): same curve, same duration, same haptic across the whole product.
- Restraint in chrome, generous whitespace, semantic & restrained color, symmetric layout, **no 3D / no custom illustration** — relentless polish on the basics.
- One opinionated style; don't compete with the OS or out-decorate the content.

---

## 13. Hero motif library (pick one per surface)

`bento_grid` (many features, no star) · `dashboard_zoom` (one star surface + callouts) · `step_flow` (a process, 3 cards) · `compare_table` (vs alternative) · `terminal_preview` (dev/CLI) · `quote_card` (one killer quote) · `definition_chips` (naming a new category) · `plug_socket` (extensibility platform) · `gamepad_split` (live tele-op: edge sliders + trim pills + center state badges).
Page roles constrain motifs: title/definition/problem/demo/process/comparison/takeaway/closing. If you can't name a page's role, it hasn't earned its place.

---

## 14. Per-setting adaptation matrix

| Setting | Input | What changes |
|---|---|---|
| **Desktop** | pointer + keyboard | Command palette + shortcuts shine; hover affordances OK; dense OK if grouped; spatial constancy. |
| **Mobile/PWA** | touch | 44px targets, bottom-reachable primary actions, sheets over dialogs, 16pt body. |
| **E-ink (reMarkable)** | finger + stylus | **No animation — snap.** Depth via weight/space not shadow. Reserve full-refresh for repaints. Bigger spacing/targets. Finger-only MouseArea over the writing canvas. Latency = next refresh. |
| **Embedded / HTTP UI** | touch on a phone over LAN | One inlined HTML file, system fonts, ≤8KB, dark default, WebSocket > polling, stream control frames at 30–60Hz (don't event-drive), trim at the control, expo in firmware not UI, PWA manifest+icon for add-to-home. |
| **TUI / CLI** | keyboard | Type roles → bold/dim; named keys not y/N; content-density caps still apply; mode always visible. |
| **Slides** | presenter | Held-constant chrome + per-deck DNA tokens; one role per slide; ≤ caps; outcome-led titles. |

---

## 15. Pre-ship checklist (ship nothing that fails these)

- [ ] Default serves the 80%; every exposed knob passed the §0 framework
- [ ] A high-ceiling path exists (palette / shortcuts / modifiers) for experts
- [ ] Headline outcome-led, ≤ 8 words; 1 primary + 1 ghost CTA above fold
- [ ] Hero is one named motif; layout/hero/accent differ from a sibling surface
- [ ] Semantic tokens only; one bold accent + one muted; **no state-by-color-alone**
- [ ] Type roles, body ≥ 14/16pt, 8-pt grid, hierarchy by weight not borders
- [ ] First visible response < 100 ms; > 1 s shows progress
- [ ] Motion serves continuity/hierarchy/hint only, ≤ 240 ms, respects reduce-motion; **e-ink = snap**
- [ ] Every gesture has a visible, teaching fallback; mode is always visible
- [ ] Destructive actions are undoable + shape/color/position-distinct
- [ ] Targets ≥ 44px, contrast ≥ 4.5:1, keyboard-reachable, reduce-motion honored
- [ ] State survives restart; empty states offer a next action
- [ ] You can name each surface's role

9+ → L3. All, with craft on every detail → L4.

---

## 16. Anti-patterns (flag immediately)

- A preference that a good default could erase. → Erase it (§0).
- A gesture/feature with no visible signifier or fallback. → Add the button.
- An **overlay drawn on top of real controls** to "restyle" them → it blocks input and adds lag; **restyle the real component in place** instead. (Hard-learned on reMarkable.)
- State shown by color only. · Two bold accents. · Animation that gates interaction. · A wall of 12 icon-bullets. · A toolbar that auto-rearranges. · A page whose role you can't name. · "OK"/"Cancel" instead of named verbs. · Important action reachable *only* by shortcut.

---

## 17. References

- Research syntheses (this session): `…/files/research/apple-hig.raw.md`, `cross-domain-ux.raw.md`, `desktop-philosophy.raw.md` — each has a "Top 25 transferable rules" + citations.
- Apple HIG (developer.apple.com), WWDC18-803 "Designing Fluid Interfaces", WWDC17-802 "Essential Design Principles".
- Norman *The Design of Everyday Things*; Rams' 10 Principles; Resnick *Lifelong Kindergarten* (low floor/high ceiling/wide walls); Kurtenbach marking menus; Raskin (modes/quasimodes); Tesler's Law; Doherty Threshold.
- GNOME HIG, KDE HIG, elementary HIG, Material Design 3, Microsoft Fluent, W3C Design Tokens.
- Prior DNA work: Spotify Keychain landing; "AI in 15" deck pipeline; desktop-robot embedded UI (live-control + PWA-on-LAN rules in §14).

*Living doc — extend as new patterns prove out. When you revise, re-grade against §15 and keep §0 as the spine.*
