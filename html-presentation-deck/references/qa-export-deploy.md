# QA, Export, And Deployment

## Browser QA

Verify:

- First load and deep-link refresh
- Previous/next boundaries
- Keyboard navigation and focus exceptions
- TOC selection and active state
- Progress calculation
- Fullscreen entry, exit, and label state
- Touch gestures without blocking vertical page gestures
- Embedded media or demos
- Reduced-motion behavior
- No clipping at representative desktop and mobile widths

Inspect the browser console and fix runtime errors.

## PDF Export

Prefer one of these paths:

1. A dedicated print route or print stylesheet plus browser "Save as PDF"
2. Playwright printing after fonts and images are ready
3. Slidev's built-in exporter when the project uses Slidev
4. Reveal.js print mode when the project uses Reveal.js

For print output:

- Render one slide per page.
- Remove interactive controls.
- Preserve backgrounds and exact aspect ratio.
- Disable transitions and animation.
- Avoid fixed overlays covering content.
- Wait for web fonts, remote images, charts, and syntax highlighting.

Document known fidelity differences. HTML-to-PDF does not preserve live interaction.

## Static Build

For Vite:

```bash
npm run build
npm run preview
```

Confirm direct navigation works with the selected routing strategy. Hash routing is simplest for static hosts.

## GitHub Pages

- Set Vite `base` to the repository subpath when needed.
- Build to `dist/`.
- Deploy with GitHub Actions Pages artifacts.
- Test asset URLs under the final subpath, not only at `/`.

## Vercel

- Use the framework's standard build command and `dist` output.
- Add a rewrite to `index.html` only when history-based SPA routing requires it.
- Prefer hash-based slide URLs when server rewrites are unavailable or unnecessary.

## Completion Report

Report:

- Framework and presentation engine
- Local development command
- Production build result
- Browser checks performed
- PDF/export command and result, if requested
- Deployment configuration added, if requested
- Known limitations or source-material fidelity gaps
