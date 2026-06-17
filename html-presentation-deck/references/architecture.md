# Architecture

## Stack Selection

### React/Vite

Choose React with Vite when the deck needs a custom design system, reusable slide components, application state, embedded demos, or Framer Motion.

Recommended shape:

```text
src/
  deck/
    deck-data.ts
    Deck.tsx
    SlideFrame.tsx
    controls/
    slides/
    hooks/
```

Keep a stable slide identifier in the deck model:

```ts
type SlideDefinition = {
  id: string
  title: string
  section?: string
  component: React.ComponentType
}
```

Use the identifier in URL state. Clamp invalid indices and preserve browser back/forward behavior.

### Slidev

Choose Slidev for Markdown-first technical talks, code highlighting, Mermaid, presenter notes, recording, built-in navigation, and PDF/PPTX/PNG export. It is Vite- and Vue-based, not React-based.

### Reveal.js

Choose Reveal.js for a lightweight HTML deck with mature navigation, fragments, overview, progress, speaker notes, and print-to-PDF support. Prefer a local package/build over CDN-only output when offline use or reproducible deployment matters.

## Navigation State

Implement:

- Arrow keys, Page Up/Down, Home, End, and Space where appropriate
- Visible previous/next buttons
- TOC or overview selection
- Hash or route synchronization
- Swipe threshold that ignores mostly vertical gestures
- Focus handling that does not steal keys from inputs, links, editors, or embedded demos

Centralize navigation in one hook or controller. Do not attach competing key listeners in individual slides.

## Fullscreen

Use the Fullscreen API after a user gesture. Detect unsupported or rejected requests and keep the deck usable without fullscreen. Update control labels from the actual fullscreen state.

## Motion

Use one transition system for slide entry/exit and a small set of content reveal variants. Animate opacity and transforms where possible. Avoid layout-heavy animation and large simultaneous stagger groups.

When reduced motion is requested:

- Remove large translations, parallax, and auto-advancing effects.
- Shorten or disable stagger.
- Preserve state changes without animation.

## Responsive Canvas

Prefer a fluid canvas with a declared aspect-ratio and safe internal padding. Scale typography with `clamp()` or design tokens, then split content if it still overflows. Do not solve overflow by applying a global transform that makes text unreadable on phones.

## Accessibility

- Use `main`, `section`, `nav`, and headings in a logical order.
- Give controls accessible names and visible focus states.
- Mark inactive slides inert or hidden from assistive technology.
- Maintain sufficient contrast and avoid color-only meaning.
- Pause autoplaying media and motion when a slide becomes inactive.

