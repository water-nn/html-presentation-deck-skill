---
name: html-presentation-deck
description: Create, redesign, or extend web-based presentation decks with slide navigation, fullscreen mode, table of contents, progress UI, responsive layouts, and motion. Use when Codex needs to turn a topic, outline, document, PPTX, screenshots, or an existing HTML/frontend project into an HTML presentation, React slide deck, Vite presentation, Tailwind/Framer Motion deck, or a deployable/exportable web slideshow.
---

# HTML Presentation Deck

Build presentation-first web apps rather than traditional PowerPoint files. Prefer React, TypeScript, Vite, Tailwind CSS, and Framer Motion for new custom decks, while preserving an existing project's framework and conventions.

## Workflow

1. Inspect the workspace, source material, and reference project before choosing an implementation.
2. Convert the content into a slide outline with one clear message per slide and intentional section rhythm.
3. Choose the smallest suitable engine:
   - Extend the existing frontend when one is provided.
   - Use React/Vite for custom visual systems, rich interactions, or reusable components.
   - Use Slidev when Markdown authoring, code demos, presenter tools, or built-in export matter more than React.
   - Use Reveal.js for a lightweight standards-based HTML deck with conventional presentation controls.
4. Define slide content as structured data or composable slide components. Keep navigation state separate from slide content.
5. Implement presentation controls and visual polish.
6. Run the app, inspect it in a browser at desktop and narrow viewports, and exercise every navigation path.
7. Build production output and, when requested, export PDF or prepare deployment.

Read [architecture.md](references/architecture.md) when choosing the stack or implementing deck behavior. Read [qa-export-deploy.md](references/qa-export-deploy.md) before final verification, PDF export, or deployment.

## Default Visual Baseline Rule

Default visual quality comes before adding more features. Unless the user explicitly asks for multiple visual directions, ship one stable default style first.

The default presentation should use a coherent premium dark tech baseline:

- deep black / graphite background
- warm-gray body text
- one primary accent, usually champagne gold or another low-saturation premium accent
- at most 1-2 support colors
- restrained cards, navigation, buttons, charts, progress, tooltips, and background motion using the same token system
- visible but quiet motion
- clear hierarchy, generous safe area, and no dashboard collage feeling

Rules:

1. The default theme must be stable before any experimental theme/tone is added.
2. New themes or tones must not break or pollute the default theme.
3. Color systems must be controlled through tokens such as CSS variables.
4. Charts, cards, navigation, buttons, progress, tooltips, and background must share the same tokens.
5. Do not sacrifice visual unity just to demonstrate more features.
6. If the user did not ask for many styles, implement one stable default tone and optionally one experimental tone.
7. Default build passing is not enough; the first impression must still feel unified, premium, clean, and presentation-ready.

## Theme / Tone Isolation Rule

Theme and tone switches must be isolated.

- `theme` controls layout or visual-system direction.
- `tone` controls palette tokens.
- Default theme must not inherit visual-theme overrides.
- Default tone must not inherit aurora or other experimental tone colors.
- Each tone must define clear tokens for text, muted text, primary accent, secondary accent, panels, borders, background motion, and chart palette.
- Components should consume CSS variables, not hard-code unrelated colors internally.
- Query variants such as `?theme=visual`, `?tone=aurora`, and `?theme=visual&tone=aurora` may exist, but the no-query default URL remains the baseline to protect.

## Visual Regression Rule

Every feature change requires a visual regression check.

- `npm run build` passing does not prove visual quality.
- After adding motion, tone, charts, navigation, or controls, re-check the default theme.
- If a user says the deck is worse than the first version, stop adding features and return to the baseline first.
- Keep a named baseline such as `Default Premium Dark Tech Baseline` or `Base Dark Tech Standard` as the reference.
- Do not let charts, backgrounds, navigation, and cards feel like different products.

## Tone Preset Practical Rule

It is fine to plan many tone presets, but a real project does not need to implement them all at once.

- Default implementation should include one stable main tone.
- Optionally include one experimental tone.
- Future tones can remain documented in the skill.
- Do not let too many tones make the current project visually noisy.
- When maintenance cost rises, reduce active tone count before compromising the default theme.

## Default Deliverables

For every React/Vite/Tailwind web presentation project, provide these files by default unless the user explicitly opts out:

- `打开预览.cmd`
- `preview.ps1` or an equivalent local preview launcher for the user's OS
- A content-editing guide such as `如何修改内容.md`
- A deployment guide such as `部署说明.md`
- A GitHub Pages workflow draft at `.github/workflows/deploy.yml` when the project is suitable for static hosting

The double-click preview must be treated as a core deliverable, not a nice-to-have. It should start the local dev server, open the browser automatically, show clear Chinese instructions that the command window must stay open, and either auto-select a free port or explain port conflicts in beginner-friendly language.

## Content Editing Entry Point

Every deck must expose a clear content-editing entry point:

- Prefer `src/slides.tsx` or `src/deck-content.tsx`.
- Use `content/slides.json` or `src/content.ts` when the deck can be safely represented as structured data.
- Keep slide content separate from navigation state and app shell code.

Document where to change:

- Main title and per-slide titles
- Body copy, cards, lists, labels, and links
- Button copy such as previous, next, fullscreen, and close labels
- Slide count: add, remove, duplicate, and reorder pages
- Colors, typography, spacing, motion, and dynamic background style
- Files beginners should edit and files they should avoid

For beginner-facing docs, say plainly that users should usually edit only the content file first.

## Required Experience

Unless the user explicitly narrows the scope, provide:

- Previous/next navigation via keyboard and visible controls
- Direct slide navigation from a table of contents or overview
- Current slide number and progress indicator
- Fullscreen entry and exit with graceful fallback
- URL state through a hash or route so a slide can be linked and refreshed
- Touch or swipe navigation when the deck targets phones or tablets
- Respect for `prefers-reduced-motion`
- A print stylesheet or documented browser/PDF export path
- Responsive scaling without clipped text, unreachable controls, or horizontal overflow

Use transitions to reinforce hierarchy, not decorate every element. Keep motion short, consistent, and reversible. Avoid scroll-driven pages disguised as slides unless the user requests that format.

## Non-Scrolling Fullscreen Rule

Deck-level scrolling is forbidden; card-level scrolling is allowed only for dense content blocks.

Every slide must fit inside one screen in normal window mode and fullscreen mode. The page, app shell, and slide stage must not require vertical scrolling to reveal slide content. Use `height: 100dvh`, `overflow: hidden`, `box-sizing: border-box`, safe padding, and `clamp()` typography/spacing tokens on the deck shell and slide containers.

When content does not fit, prefer these fixes in order:

- Edit the copy down.
- Split the material into more slides.
- Group the content into cards.
- Use two-column, three-column, or grid layouts.
- Reduce typography and spacing with responsive tokens.

Fullscreen must adapt layout, not merely call the browser Fullscreen API. Listen for `fullscreenchange` or use fullscreen-aware class names/selectors so layout recalculates after entering or exiting fullscreen.

## 16:9 Stage Layout Standard

HTML PPT must behave like a presentation stage, not a normal scrolling webpage. The outer app may use `100dvw` by `100dvh`, but the actual slide must live inside a clear 16:9 stage.

Rules:

- Use a dedicated stage area that accounts for top controls, side navigation, bottom progress, and safe padding.
- The slide frame should use `aspect-ratio: 16 / 9`.
- The slide frame must be horizontally and vertically centered inside the available stage area.
- Use `width: min(...)`, `max-height: 100%`, or height-derived width calculations so the frame scales to the available area.
- Fullscreen, ultrawide, 16:9 desktop, and smaller laptop viewports must remain stable.
- Navigation and controls are safe-area UI, not part of the slide's main composition.

## Visual Centering and Safe Area Rule

Default slide content should feel visually centered inside the 16:9 frame. Top bars, bottom progress, side Agenda, slide counters, and browser chrome must not cover or squeeze the slide message.

When designing layout:

- Keep the main message in a balanced visual center.
- Reserve internal slide padding with responsive tokens.
- Avoid large accidental empty bands above content.
- Avoid clipped title, cards, charts, page numbers, or progress indicators.
- Special cover or closing pages may use a deliberate editorial composition, but that should be an explicit design choice.

## No Default Bottom Anchoring

Do not default slide content to the bottom. Avoid `align-items: end`, `align-content: end`, or `justify-content: end` on slide-level containers unless the slide explicitly requests a bottom-anchored layout.

Support slide layout metadata such as:

```ts
type SlideLayout =
  | "center"
  | "title"
  | "split"
  | "dashboard"
  | "grid"
  | "closing"
```

Default to `center`, or infer a balanced layout from slide kind. Bottom anchoring is allowed only for intentional visual pages and should not be the majority pattern.

## Card-Level Scroll Exception

Only local dense-content containers may scroll, such as `.card-scroll` or `.scrollable-card`.

Allowed examples:

- Long explanation blocks
- Long lists
- Tables
- Code blocks
- Source excerpts
- Data details

Rules for local scrolling:

- The overall page and slide remain non-scrolling.
- The scrollable card must have an explicit max height, such as `max-height: clamp(...)`.
- Use `overflow-y: auto` and `overscroll-behavior: contain`.
- Style scrollbars so they are subtle and consistent with the deck.
- Prefer splitting or editing content before adding local card scrolling.

## Typography Standard

For Chinese HTML presentation decks, use one high-quality Chinese sans-serif font stack across the whole app:

```css
font-family:
  "Source Han Sans SC",
  "Noto Sans SC",
  "PingFang SC",
  "Alibaba PuHuiTi",
  "阿里巴巴普惠体",
  "思源黑体",
  system-ui,
  sans-serif;
```

Use the same family for titles, body text, notes, controls, agenda, charts, tables, and labels. Express hierarchy with `font-weight`:

- `300` for quiet supporting notes
- `400` for body text
- `500` for emphasized body text
- `700` for headings
- `900` for hero and cover titles

Do not specify Microsoft YaHei, DengXian, SimSun, Times New Roman, or default serif stacks. Do not commit font files unless the user explicitly asks for bundled fonts. Online fonts are optional only when local fallbacks remain intact.

## Line Height Standard

Use `line-height: 1.3` as the default text rhythm.

Apply it to `body`, headings, paragraphs, list items, buttons, navigation, card copy, chart labels, and table text. Avoid mixing `1.1`, `1.2`, `1.5`, `1.6`, or other line heights. If a rare decorative hero title must differ, add a short code comment explaining why.

## Presentation Readability Standard

Design for an audience reading from a distance, not only for someone sitting close to a laptop.

Minimum practical font sizes:

- Cover hero title: at least `56px`
- Standard slide title: at least `44px`
- Secondary heading: at least `30px`
- Body text: at least `22px`
- Card body text: at least `20px`
- Chart axis, legend, and labels: at least `16px`
- Table body text: at least `16px`
- Notes, tags, page numbers, and navigation text: at least `13px`, and never as the only carrier of main information

If important body copy must shrink below `18px` to fit, split the content into more slides instead of reducing text size.

## Keyboard Navigation Standard

Slide navigation must support mouse and keyboard through the same navigation logic:

- Mouse click previous/next buttons
- `ArrowRight` for next
- `ArrowLeft` for previous
- `ArrowDown` for next
- `ArrowUp` for previous
- `PageDown` for next
- `PageUp` for previous
- `Space` for next
- `Home` for first slide
- `End` for final slide

Keyboard events must not interfere with input fields, textareas, selects, links, buttons, or editable regions. Boundary navigation at the first and last slide must be safe and must not throw.

## Interaction Quality

Do not ship a static-feeling deck. Every element that looks interactive must have perceivable feedback:

- Buttons, links, cards, tags, TOC/Agenda items, and navigation controls need hover states.
- Add active or pressed states for click/tap feedback.
- Add `focus-visible` states for keyboard accessibility.
- Add disabled states for unavailable previous/next actions.
- Use `cursor: pointer` for clickable controls.
- Use 150ms-300ms transitions for transform, border, background, shadow, color, and opacity changes.
- Cards may lift slightly, gain a glow, highlight their border, or deepen shadow on hover.
- Agenda/TOC items must highlight clearly on hover and active selection.
- Fullscreen and pagination controls must have obvious hover and pressed feedback.

Interaction polish must remain tasteful and should not reduce readability.

## Dynamic Background

Default to an advanced but restrained dynamic background rather than a flat static dark page:

- Use animated gradient mesh, slow radial glow, aurora blobs, subtle grid, noise, or sparse particles.
- Keep motion slow and premium; avoid cheap sci-fi effects.
- Do not place bright or busy motion directly behind dense text.
- Prefer CSS `transform` and `opacity` for animation performance.
- Support `prefers-reduced-motion` and reduce or disable background animation when requested by the OS.

## Dynamic Background Standard

Dynamic background must be real, visible, and restrained across the whole deck, not just the cover slide. Prefer named layers such as `DynamicBackground`, `.deck-background`, `.aurora-layer`, `.stage-light-layer`, `.grid-layer`, and `.noise-layer`.

Suitable motion:

- Slow radial glows
- Aurora gradients
- Subtle grids
- Noise texture
- Stage-light sweeps
- Champagne-gold shimmer

Animation should use CSS `transform`, `opacity`, and `background-position` where possible. Keep contrast low enough that text remains readable. Always include a `prefers-reduced-motion: reduce` branch that lowers or disables motion.

## Visible Dynamic Background Standard

Do not validate dynamic backgrounds only by checking that DOM layers exist. The motion should be perceptible to a human within 3-5 seconds while staying premium and quiet.

Recommended layer names:

- `.deck-bg-base`
- `.deck-bg-grid`
- `.deck-bg-aurora`
- `.deck-bg-spotlight`
- `.deck-bg-noise`

Use slightly stronger but controlled opacity, larger drift distances, stage-light sweeps, champagne radial glow, and subtle grid/noise. Avoid game-like or cheap sci-fi effects. Run a visual check by leaving the page still for several seconds and confirming light movement is visible without hurting text contrast.

When a user asks for stronger launch-event presence, increase the background motion by roughly `1.5x-2x` while preserving text contrast. Prefer named CSS variables such as:

- `--bg-motion-opacity`
- `--bg-glow-opacity`
- `--bg-sweep-opacity`
- `--bg-particle-opacity`

Suitable additions include command light sweeps, agent node pulses, subtle particle drift, slow aurora ribbons, and cursor-like scan glows. Within 2-3 seconds, the background should feel alive without becoming the focal point. Do not rely only on DOM existence checks; sample computed transforms/background positions over time, compare screenshots, or visually inspect motion.

## Data Visualization Components

New React/Vite decks should provide or plan reusable data display components, even when the first content pass is mostly text:

- Bar chart slide/component
- Line chart slide/component
- Pie or donut chart slide/component
- Table slide/component
- Metric card slide/component
- Comparison matrix slide/component
- Timeline slide/component

Prefer lightweight SVG and CSS implementations before adding charting dependencies. If a third-party chart library is necessary, explain why and choose a stable library.

## Chart Visual Integration Standard

Charts and tables must look like part of the deck, not external widgets.

Use the same visual system as the presentation:

- Dark surfaces
- Champagne-gold emphasis
- Warm-gray grid lines
- Low-saturation support colors
- Rounded cards
- Restrained shadows
- Unified font stack
- `line-height: 1.3`
- Hover, active, and focus-visible feedback

Axes, legends, labels, tooltips, table headers, and table cells must follow the same typography, spacing, and color system. Do not ship browser-default table styles or unstyled library defaults. Charts must fit the 16:9 slide layout without causing deck-level scrolling.

## Presentation-Style Data Visualization Layout

Data slides should feel like presentation pages, not dense dashboards.

Rules:

- Do not cram too many charts into one slide.
- A single data slide should usually contain at most 2 main charts plus 2 metric cards, 3 medium charts, or 1 large chart plus 1 table.
- If the deck needs to show bar, line, donut, table, and metric components, split them into multiple slides.
- Use `minmax(0, 1fr)` grid tracks and `height: 100%` chart containers so charts fit the 16:9 frame.
- SVG charts must respond to container size.
- Long tables may use card-level scrolling, but the page itself must not scroll.
- Charts should be centered and balanced like slide content, not packed edge-to-edge like an app dashboard.

## Collapsible Navigation / Agenda

Right-side or side Agenda navigation should be compact by default and expandable on demand:

- Default to a narrow rail or collapsed state when space is tight.
- Provide a clear toggle button.
- Allow the user to expand and collapse the full agenda.
- In collapsed mode, keep only a small Agenda icon, page number, or narrow navigation rail.
- Expanded desktop width should usually stay at or below `220px`.
- Small screens should use an overlay, top menu, or bottom menu rather than squeezing slide content.
- Agenda items need hover, active/pressed, focus-visible, and current-slide states.
- Expand/collapse animation should be smooth, around 150ms-300ms.
- Navigation must not force slide content to scroll or overflow.
- Fullscreen mode must keep navigation compact enough that it does not cover the main message.

## Navigation-to-Stage Balance

Navigation must not break the centered 16:9 stage. Collapsed navigation should usually be 56px-72px wide. Expanded navigation should usually remain 200px-220px wide. When expanded navigation would distort the stage, prefer overlay behavior or a lightweight rail instead of squeezing the slide.

The slide frame must be recalculated and centered in the remaining available space. In fullscreen mode, navigation should feel lighter, not heavier.

## Hover Navigation Standard

Default navigation should not occupy a large permanent agenda area. Prefer a slim hover navigation rail:

- Collapsed width should usually be `48px-64px`.
- Hovering over the navigation region expands it automatically.
- Moving the pointer away collapses it automatically.
- Expanded width should usually be at or below `200px`.
- Hover-expanded navigation should overlay the stage as a floating glassmorphism panel, not reflow the main stage.
- Hover-expanded navigation must not squeeze, resize, deform, or make the slide frame jump.
- Pinned navigation may reflow and recenter the stage in the remaining safe area.
- When pinned navigation is cancelled, return to overlay hover behavior.
- The expanded menu may overlay the stage, but must not cover the current slide's core message.
- If overlay navigation risks covering content, make the navigation narrower/lighter or reserve only a slim collapsed safe margin.
- Highlight the current slide.
- Agenda items need hover, active/pressed, and focus-visible states.
- Optionally provide a pin control to keep navigation open.
- Fullscreen mode must keep hover navigation usable.
- The Agenda page, hover navigation, progress, and slide count must come from the same slide data source.

## Navigation Pin Control Standard

Navigation pin/fixed controls should feel like part of the presentation system, not temporary debug buttons.

- Do not use rough one-character visible labels such as `钉` or `锁` as the main control copy.
- Prefer a compact icon-like control such as `⌖`, `●`, `Pin`, `Pinned`, or clear Chinese labels such as `固定` and `收起`.
- Provide accessible labels or titles such as `固定目录` and `取消固定`.
- Keep at least `12px-16px` spacing between the rail label and the pin control.
- Use the same visual language as the nav: rounded shape, refined border, hover, active, focus-visible, and current-state highlight.
- The control must not touch the rail edge or feel like an afterthought.

## Decorative Label Safe Area Rule

Decorative labels, section tags, running headers, and vertical English labels must never reduce content readability.

- Do not place section labels on top of slide body text, charts, cards, or key visual elements.
- Prefer top eyebrow areas, top-left small tags, a bottom safe area above progress, or the navigation panel.
- If a vertical label is used, reserve a dedicated safe area for it.
- Keep decorative labels low-contrast, small, and restrained.
- Decorative labels must not be the only carrier of important information.
- During QA, inspect data slides and split layouts for labels overlapping charts or dense content.

## Split Layout Balance Standard

Split slides must balance visual weight, not merely divide the canvas into two columns.

- Left and right sides do not need equal information volume, but they should feel intentionally balanced.
- Pair text with a diagram, workflow, chart, visual anchor, card cluster, large number, line frame, or background light mass.
- Avoid one side carrying a heavy chart while the other side has only weak explanatory copy.
- Avoid a large heading occupying one side while the other side feels empty.
- Choose grid ratios based on content, such as `52/48`, `45/55`, or `40/60`.
- Every split slide should have clear visual hierarchy and one deliberate focal point.
- Inspect all split layouts during visual QA for imbalance, accidental empty space, and bottom anchoring.

## Theme Variant Standard

When the deck benefits from comparing visual directions, support theme variants in the same project instead of duplicating content.

- Prefer query parameters such as `?theme=editorial` and `?theme=visual`.
- The default theme should remain stable and presentation-ready.
- Theme variants must share the same slide content source unless the user explicitly asks for separate decks.
- Theme switching should not break hash navigation, printing, GitHub Pages base paths, keyboard controls, fullscreen, or agenda sync.
- README should document available themes and example URLs.

## Visual Theme / Product Demo Style Standard

A `visual` or product-demo theme should be visibly different from an editorial/card-based theme.

- Use more system diagrams, agent nodes, workflow maps, radial system diagrams, floating panels, connection lines, command-center layouts, layered glass UI, and spatial depth.
- Increase background motion and visual structure while preserving readability.
- Avoid turning the page into a crowded dashboard collage.
- Prioritize cover, statement, architecture, tool ecosystem, workflow, data story, skill capability, and closing slides for visual-theme differentiation.
- Keep all presentation readability, no-scroll, 16:9, accessible interaction, and reduced-motion standards intact.

## Chart Enter Animation Standard

Data visualizations should enter like a polished product-launch moment, not appear as static widgets.

- Trigger chart animations only when the slide becomes active/current.
- Replaying animations when returning to a slide is usually preferred for demos.
- Progress bars should animate from `0` to the target value over roughly `700ms-1200ms`.
- Bar charts should grow from `0` to target width/height over roughly `800ms-1200ms`.
- Line charts should draw left-to-right over roughly `900ms-1400ms`, using SVG stroke dash techniques where practical.
- Donut/pie charts should animate arcs from `0` to target proportions over roughly `900ms-1400ms`.
- Metric numbers may count up over roughly `700ms-1000ms`.
- Tables may use subtle staggered fade-in.
- Use restrained easing such as `cubic-bezier(0.22, 1, 0.36, 1)` or `ease-out`.
- Avoid bounce, gimmicky loading effects, game-like motion, or excessive decoration.
- Support `prefers-reduced-motion: reduce`; show final values immediately or use only a very light fade.
- Prefer React state, CSS transitions, SVG `stroke-dasharray` / `stroke-dashoffset`, transforms, and opacity before adding animation libraries.

## Tone Preset Standard

When generating HTML presentations, offer or choose a coherent tone preset instead of changing colors ad hoc. Each tone should define background, main text, muted text, primary accent, secondary accent, panel/card color, border color, background motion colors, and chart palette. Buttons, navigation, charts, tooltip, cards, progress, and backgrounds must all follow the same token system.

Recommended tone presets:

1. **Obsidian Champagne / 黑曜香槟**: deep black and warm gray, champagne-gold accents, soft warm light. Best for product launches, brand roadshows, fashion-tech, formal proposals, project reports, and brand storytelling.
2. **Graphite Aurora / 石墨极光**: graphite black with low-saturation cyan/green aurora glow. Best for AI, toolchains, Agent systems, technical architecture, and workflow diagrams.
3. **Ink Cyan / 墨黑青蓝**: ink-dark background, cyan-blue accents, visible line frames and grids. Best for data reports, technical analysis, research, and SaaS presentations.
4. **Velvet Burgundy / 丝绒酒红**: deep burgundy/black-purple background with rose-gold or champagne accents. Best for dramatic launches, creative proposals, art, film, and content projects.
5. **Porcelain Editorial / 象牙杂志**: ivory and warm gray, black text, gold or dark-gray emphasis. Best for brand manuals, editorial layouts, content plans, and portfolios.
6. **Sandstone Gallery / 沙岩展厅**: sandstone, warm beige-gray, dark brown, and soft gold. Best for culture, exhibitions, spatial planning, and brand stories.
7. **Mono Museum / 黑白美术馆**: black, white, and gray with one restrained accent. Best for design proposals, portfolios, research conclusions, and serious talks.
8. **Signal Lime / 信号青柠**: dark background with restrained low-saturation lime signal color. Best for product demos, developer tools, efficiency products, and growth strategy.

Support query-parameter tone switches such as `?tone=champagne`, `?tone=aurora`, or `?tone=ivory` when feasible. If runtime switching is too costly, at least implement clean CSS tokens so tone changes are straightforward later. Tone choices must not compromise no-scroll layout, readable font sizes, 16:9 stage behavior, visible-but-subtle motion, or chart consistency.

## Chart and Layout Refinement Checklist

Before final delivery, inspect charts and visual layouts for narrative quality:

- Chart titles read like conclusions, not component names.
- Chart whitespace is generous and intentional.
- Chart colors match the deck tone tokens.
- Axis labels, legends, values, and table text remain legible from a presentation distance.
- Charts are not crowded or placed too close to unrelated text.
- Text and charts have clear primary/secondary hierarchy.
- Split layouts feel visually balanced.
- Main visuals are not accidentally all left-aligned or bottom-anchored.
- The page feels like a presentation slide, not a dashboard collage.
- Decorative labels do not overlap content.
- Visual theme variants are distinct enough to justify their existence.

## Single Source of Truth for Agenda

Agenda pages, side navigation, progress, slide sections, and page counts must come from the same slide data model. Do not hand-write two separate agenda lists.

Use a clear data shape such as:

```ts
type Slide = {
  id: string
  title: string
  section?: string
  navTitle?: string
  agendaTitle?: string
  kind?: "cover" | "agenda" | "content" | "data" | "closing"
}
```

If the agenda page shows slide titles, side navigation should show the corresponding titles in the same order. If the agenda page groups by section, side navigation should use the same grouping. README or content-editing docs must tell users to edit the slide data source, not navigation component copy.

## Fullscreen Adaptation

Fullscreen mode must adapt the layout, not merely call the browser Fullscreen API:

- Use `100dvw`, `100dvh`, or equivalent dynamic viewport units.
- Keep a 16:9 presentation logic while allowing safe scaling for the actual screen.
- Use `clamp()` or responsive tokens for title size, body text, spacing, and card gaps.
- Avoid text becoming too large, too small, clipped, or pressed against screen edges.
- Listen for `fullscreenchange` and update state/class names when needed.
- Test or inspect fullscreen layout before final delivery when browser tooling is available.

## Optional Companion Design Skills

Before generating, redesigning, or optimizing an HTML PPT, check whether any of these optional companion design skills are installed:

- `ui-ux-pro-max-skill`
- `design-ux-researcher`
- `design-ui-designer`
- `frontend-design`

These skills provide optional design backing, not hard dependencies. If none are installed, do not fail, do not interrupt the task, and continue with this skill's built-in design rules. In the final output, state: "未检测到外部 UI/UX 设计类 skill，本次按 html-presentation-deck 内置设计标准执行。"

If any companion design skill is installed, actively reference its design principles before and during HTML PPT work. Focus especially on:

- Information architecture
- Page visual hierarchy
- Layout rhythm
- Whitespace and alignment
- Color system
- Typography hierarchy
- Interaction feedback
- Accessibility
- Component consistency
- Chart visual unity
- Presentation-room readability
- Responsive and fullscreen adaptation
- Visual QA

After each HTML PPT project, run a Design QA Pass. Check:

- The default theme is unified.
- The tone is not visually chaotic.
- The page feels like a presentation stage, not a normal webpage.
- Nothing is accidentally off-center, unbalanced, bottom-stuck, clipped, or covered.
- Charts feel like part of the presentation document.
- Navigation is refined and does not pressure the main content.
- Background motion is visible but does not compete with content.
- Font sizes are suitable for audience reading from a distance.

If companion design skills were used, state in the final output: "已参考本机设计类 companion skills 进行 Design QA。"

## Content And Layout

- Establish audience, duration, slide count, tone, aspect ratio, and call to action from the prompt or source material.
- Create a narrative arc: opening, context, key sections, synthesis, and closing.
- Keep body copy presentation-sized. Split dense material instead of shrinking text.
- Use semantic HTML and accessible labels for controls.
- Use reusable primitives for title, section, comparison, quote, metric, timeline, demo, and closing slides.
- Reserve safe areas for navigation, progress, and browser chrome.
- Use images, diagrams, charts, and code only when they carry meaning.

## Layout Variety Standard

Avoid building every slide as "title plus cards." A polished HTML presentation should mix several slide patterns by default:

- Hero cover
- Agenda
- Big statement
- Split layout
- Magazine spread
- Timeline
- Comparison matrix
- Data story
- Dashboard-lite
- Process flow
- System architecture
- Roadmap
- Risk and decision
- Closing

Each page should have one clear visual focus. Do not create a rough dashboard collage just because the medium is HTML.

## Existing Projects

When a reference HTML or frontend project exists:

1. Read its package manifest, routes, styling setup, and component conventions.
2. Reuse its tokens, fonts, utilities, and installed dependencies.
3. Preserve unrelated behavior and avoid replacing the app architecture solely to match this skill's defaults.
4. Add presentation behavior in a focused module or route.

## PPTX And Documents

Treat PPTX conversion as content and asset extraction, not guaranteed pixel-perfect translation.

- Extract slide text, speaker notes, images, and ordering.
- Rebuild layouts as responsive web components.
- Preserve charts as data-driven graphics when practical; otherwise use exported images.
- Record any unsupported media, fonts, or animation semantics.
- Use a fidelity-audit workflow when exact visual comparison is required.

## Verification

Do not stop after generating files.

- Run the repository's formatter, type checker, tests, and production build when available.
- Open the deck in a browser and verify keyboard, click, touch, hash/route restoration, TOC, progress, and fullscreen.
- Check the first slide, a dense middle slide, and the final slide at desktop and narrow viewport sizes.
- Confirm reduced-motion and print/PDF output are legible.
- Report the run, build, export, and deployment commands used.

## Responsive Fit Audit

Build passing is not enough. Run a layout audit in representative viewports when browser tools are available:

- `1366x768`
- `1440x900`
- `1920x1080`
- A wide or ultrawide viewport similar to the user's screenshot when relevant

Check:

- `document.documentElement.scrollHeight <= document.documentElement.clientHeight`
- `document.body.scrollHeight <= document.body.clientHeight`
- The slide frame bounding box is inside the viewport.
- Slide content is not hidden behind navigation or progress UI.
- Normal mode has no page-level scrolling.
- Fullscreen or simulated fullscreen has no page-level scrolling.
- Content is not accidentally bottom-anchored.
- Dynamic background motion is visible after a few seconds.

## Visual QA Checklist

Before delivery, inspect or programmatically check:

- HTML PPT feels like a centered 16:9 stage, not a normal webpage.
- Slide frame is centered in the available safe area.
- Default content is not bottom-anchored.
- Titles, cards, charts, page counters, and progress UI are not clipped.
- Data slides are not overcrowded; split them if needed.
- Navigation does not visually dominate or compress the stage.
- Dynamic background is visible but does not reduce readability.
- Common desktop viewports have no page-level scroll.
- Any remaining visual-risk slides are called out for human review.

## Validation Checklist

Before delivery, verify:

- Normal window mode has no deck-level vertical scrolling.
- Fullscreen mode has no deck-level vertical scrolling.
- Every slide's main content is fully visible in one screen.
- Dense content uses only card-level scrolling, when truly necessary.
- Fonts are unified around Source Han Sans SC, Noto Sans SC, PingFang SC, Alibaba PuHuiTi, or equivalent high-quality Chinese sans-serif fallbacks.
- Microsoft YaHei, DengXian, SimSun, Times New Roman, and serif defaults are not specified.
- Global `line-height` is `1.3`.
- Presentation readability minimum font sizes are respected.
- Mouse previous/next navigation works.
- Left/right keyboard navigation works.
- Up/down keyboard navigation works.
- PageUp/PageDown, Space, Home, and End work.
- Dynamic background is visibly present and supports reduced motion.
- Side navigation is collapsible.
- Hover navigation expands and collapses without squeezing the stage.
- Hover navigation overlays without reflowing the slide frame; pinned navigation may reflow and recenter the stage.
- Navigation pin/fixed controls are polished and do not use rough labels such as `钉` or `锁`.
- Decorative labels stay inside safe areas and never overlap primary content.
- Split layouts are visually balanced.
- Side navigation uses a reasonable share of screen space.
- Slide frame follows a centered 16:9 stage model.
- Default slide content is visually centered, not bottom-anchored.
- Data visualization pages are presentation-style and not overcrowded.
- Chart pages have data enter animations when they become active.
- Progress bars can animate from `0` to the target value.
- Bar charts can animate from `0` to target height/width.
- Line charts can draw from left to right.
- Donut charts can animate from `0` to target proportions.
- Metric numbers can count up.
- Chart animation triggers only for the active slide.
- Reduced motion lowers or disables chart and background animation.
- Tone tokens stay consistent across charts, navigation, cards, buttons, progress, and backgrounds.
- Side navigation content matches the agenda page.
- Chart and table components exist or are explicitly planned.
- Chart and table visuals match the deck design system.
- `npm run build` passes.
- After GitHub Pages deployment, the remote URL returns HTTP 200.

## Remote Viewing

Always provide a remote viewing path in addition to local double-click preview:

- Option A: Vercel deployment
- Option B: GitHub Pages deployment
- Option C: deployment instructions only

Default rules:

- If the project has a GitHub remote and the user permits push/deploy actions, prepare GitHub Pages.
- If the Vercel CLI exists and the user is logged in, Vercel can be used to generate a preview link.
- If credentials, permissions, or remotes are missing, do not pretend deployment succeeded. Explain the missing condition.

For GitHub Pages:

- Check `git remote -v`.
- Infer the repository name from the remote when possible.
- Set Vite `base` correctly for the repository subpath, preferably using GitHub Actions environment variables.
- Create `.github/workflows/deploy.yml`.
- Build to `dist`.
- Explain that the final link usually looks like `https://<GitHub用户名>.github.io/<仓库名>/`.

## GitHub Pages 远程发布标准流程

When the user wants a remotely viewable HTML PPT link, prefer GitHub Pages before other static hosts unless the user asks otherwise. Treat the following successful deployment as the reference pattern:

- Project: `agent-skill-html-deck`
- Repository: `https://github.com/water-nn/agent-skill-html-deck`
- Pages URL: `https://water-nn.github.io/agent-skill-html-deck/`
- Content entry: `src/slides.tsx`
- Local preview entry: `打开预览.cmd`
- Build command: `npm run build`

Standard GitHub Pages rules:

1. Project-repository Pages URLs usually use `https://<GitHub用户名>.github.io/<仓库名>/`.
2. For Vite deployments to project-repository Pages, always check `vite.config.ts` and set `base` to the repository subpath. Example: for repository `agent-skill-html-deck`, use:

   ```ts
   export default defineConfig({
     base: '/agent-skill-html-deck/',
     plugins: [react()],
   })
   ```

3. Create or verify `.github/workflows/deploy.yml`.
4. The workflow must build with `npm ci` and `npm run build`, then deploy the `dist` folder with `actions/upload-pages-artifact` and `actions/deploy-pages`.
5. The workflow must include:

   ```yaml
   permissions:
     contents: read
     pages: write
     id-token: write
   ```

6. Enable GitHub Pages Source as `GitHub Actions`. Use `gh api` when possible; otherwise give the manual path: GitHub repository page -> Settings -> Pages -> Build and deployment -> Source -> GitHub Actions.
7. After deployment succeeds, create or update `REMOTE_URL.txt` with the final Pages URL.
8. Update `部署说明.md` with local preview, content editing, rebuild, push-to-update, remote URL, blank-page troubleshooting, and Actions failure guidance.
9. Remind the user:
   - Edit content mainly in `src/slides.tsx`.
   - Preview locally by double-clicking `打开预览.cmd`.
   - Update the remote site with `git add`, `git commit`, and `git push`.
   - After push, GitHub Actions automatically redeploys the Pages site.
10. If GitHub CLI is not logged in, no remote exists, permissions are missing, or `workflow` scope is insufficient, do not pretend deployment succeeded. State the exact blocking step.
11. If pushing `.github/workflows/deploy.yml` fails because the token lacks `workflow` scope, tell the user to reauthorize GitHub CLI with workflow permission, for example `gh auth refresh -h github.com -s workflow`, then retry the push.

For Vercel:

- Check whether `vercel` CLI exists.
- If missing, explain how to install it.
- If logged in and deployment is authorized, deploy and write the resulting URL to `DEPLOY_URL.txt`.
- If deployment cannot be completed, write `部署说明.md` or equivalent instructions.
