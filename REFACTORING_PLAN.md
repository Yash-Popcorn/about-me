# Refactoring Plan for About-Me Portfolio

This document outlines a structured plan to refactor the codebase from a monolithic single-file application into a maintainable, scalable architecture.

## Current State

- **Single file:** All HTML, CSS, and JavaScript in `index.html` (252 lines)
- **No separation of concerns:** Markup, styles, and behavior are intermixed
- **Hardcoded content:** Movies, essays, and links embedded directly in HTML
- **Inline event handlers:** Using `onclick` attributes instead of event listeners

---

## Phase 1: Separate Concerns (Foundation)

### 1.1 Extract CSS to `styles.css`
**Goal:** Separate styling from structure for better caching and maintainability.

**Tasks:**
- [ ] Create `styles.css` file
- [ ] Move all CSS from `<style>` tag to external file
- [ ] Link stylesheet in `index.html`
- [ ] Verify no style regressions

**Files to create:**
```
styles.css
```

### 1.2 Extract JavaScript to `script.js`
**Goal:** Separate behavior from structure.

**Tasks:**
- [ ] Create `script.js` file
- [ ] Move all JavaScript from `<script>` tag to external file
- [ ] Replace inline `onclick` handlers with `addEventListener()`
- [ ] Add `DOMContentLoaded` wrapper for safety
- [ ] Link script with `defer` attribute in `index.html`

**Files to create:**
```
script.js
```

### 1.3 Clean Up `index.html`
**Goal:** Leave only semantic HTML structure.

**Tasks:**
- [ ] Remove `<style>` and `<script>` blocks
- [ ] Add proper `<link>` and `<script>` references
- [ ] Verify page loads correctly with external files

---

## Phase 2: CSS Architecture

### 2.1 Implement CSS Custom Properties
**Goal:** Create a consistent theming system.

**Tasks:**
- [ ] Define color variables:
  ```css
  :root {
    --color-bg: #1D1D1B;
    --color-text: #FFFFFF;
    --color-muted: #646464;
    --color-link: #646464;
    --color-link-hover: #FFFFFF;
  }
  ```
- [ ] Define spacing scale:
  ```css
  :root {
    --space-xs: 4px;
    --space-sm: 8px;
    --space-md: 16px;
    --space-lg: 24px;
    --space-xl: 32px;
  }
  ```
- [ ] Replace hardcoded values with variables
- [ ] Remove unused variables (e.g., `--flower-size`)

### 2.2 Remove Unused CSS
**Goal:** Eliminate dead code.

**Tasks:**
- [ ] Remove `.flower` class (unused)
- [ ] Audit all classes for usage
- [ ] Document remaining classes

### 2.3 Organize CSS Structure
**Goal:** Logical grouping of styles.

**Tasks:**
- [ ] Order CSS sections:
  1. Variables/Custom Properties
  2. Reset/Base styles
  3. Layout styles
  4. Component styles
  5. Utility classes
  6. Media queries

---

## Phase 3: JavaScript Architecture

### 3.1 Remove Inline Event Handlers
**Goal:** Separate behavior from HTML markup.

**Current (anti-pattern):**
```html
<span onclick="openEssay('no-more-overthinking')">audio</span>
```

**Target:**
```html
<span data-essay="no-more-overthinking">audio</span>
```
```javascript
document.querySelectorAll('[data-essay]').forEach(el => {
  el.addEventListener('click', () => openEssay(el.dataset.essay));
});
```

### 3.2 Implement Module Pattern
**Goal:** Avoid global namespace pollution.

**Tasks:**
- [ ] Wrap code in IIFE or use ES modules
- [ ] Create clear API for essay modal functionality
- [ ] Document public functions

### 3.3 Error Handling
**Goal:** Graceful degradation for external resources.

**Tasks:**
- [ ] Add font loading fallback
- [ ] Improve image error handling with placeholder
- [ ] Add console logging for debugging

---

## Phase 4: Data-Driven Content

### 4.1 Create Content Data Structure
**Goal:** Separate data from presentation.

**Tasks:**
- [ ] Create `data.js` with content:
  ```javascript
  const siteData = {
    movies: [
      { title: "Anora", tmdbId: "1064213" },
      { title: "Woman of the Hour", tmdbId: "1047373" },
      // ...
    ],
    essays: {
      "no-more-overthinking": {
        title: "no more overthinking",
        audioFiles: ["genesis.mp3", "run-that-back-turbo.mp3", "bizet.mp3"]
      }
    },
    links: [
      { name: "LinkedIn", url: "https://linkedin.com/in/yashjseth" },
      { name: "yash@popcorn.video", url: "mailto:yash@popcorn.video" }
    ]
  };
  ```

### 4.2 Create Render Functions
**Goal:** Dynamic content generation.

**Tasks:**
- [ ] Create `renderMovies(movies)` function
- [ ] Create `renderEssays(essays)` function
- [ ] Create `renderLinks(links)` function
- [ ] Replace hardcoded HTML with dynamic rendering

---

## Phase 5: Accessibility Improvements

### 5.1 Semantic HTML
**Goal:** Improve screen reader experience.

**Tasks:**
- [ ] Replace `<span class="media-item">` with `<button>` for interactive items
- [ ] Add proper heading hierarchy
- [ ] Use `<article>` for essay content
- [ ] Add `role` attributes where semantic elements aren't appropriate

### 5.2 ARIA Labels
**Goal:** Enhance accessibility for assistive technologies.

**Tasks:**
- [ ] Add `aria-label` to interactive elements
- [ ] Add `aria-modal="true"` to essay overlay
- [ ] Add `aria-hidden` to decorative elements
- [ ] Add focus trap for modal

### 5.3 Keyboard Navigation
**Goal:** Full keyboard accessibility.

**Tasks:**
- [ ] Ensure all interactive elements are focusable
- [ ] Add visible focus indicators
- [ ] Implement focus trap in modal
- [ ] Test with screen reader

---

## Phase 6: Feature Restoration

### 6.1 Movie Poster Hover Cards
**Goal:** Restore and fix the movie poster feature (reverted in PR #4).

**Tasks:**
- [ ] Analyze scroll conflict that caused revert
- [ ] Implement poster cards with proper positioning
- [ ] Use CSS `position: fixed` or adjust container height handling
- [ ] Add image lazy loading
- [ ] Add fallback for failed image loads

### 6.2 Image Optimization
**Goal:** Improve performance.

**Tasks:**
- [ ] Add lazy loading to images
- [ ] Consider local caching of poster URLs
- [ ] Add WebP format support with fallbacks

---

## Phase 7: Build & Tooling (Optional)

### 7.1 Development Server
**Goal:** Improved development experience.

**Tasks:**
- [ ] Add simple dev server (e.g., `npx serve`)
- [ ] Add live reload for development
- [ ] Document development workflow

### 7.2 Production Build
**Goal:** Optimized production assets.

**Tasks:**
- [ ] Add CSS minification
- [ ] Add JavaScript minification
- [ ] Add HTML minification
- [ ] Consider bundling with simple tool (esbuild, parcel)

---

## Final Project Structure

```
about-me/
├── index.html          # Semantic HTML structure only
├── styles.css          # All styling
├── script.js           # Interactive behavior
├── data.js             # Content data (optional)
├── assets/
│   ├── audio/
│   │   ├── bizet.mp3
│   │   ├── genesis.mp3
│   │   └── run-that-back-turbo.mp3
│   └── images/         # Local images if needed
├── REFACTORING_PLAN.md # This document
└── README.md           # Project documentation
```

---

## Implementation Order

| Priority | Phase | Estimated Effort | Impact |
|----------|-------|------------------|--------|
| 1 | Phase 1: Separate Concerns | Low | High |
| 2 | Phase 3: JS Architecture | Low | Medium |
| 3 | Phase 2: CSS Architecture | Low | Medium |
| 4 | Phase 5: Accessibility | Medium | High |
| 5 | Phase 4: Data-Driven Content | Medium | Medium |
| 6 | Phase 6: Feature Restoration | Medium | Low |
| 7 | Phase 7: Build & Tooling | High | Low |

---

## Success Criteria

- [ ] All CSS in separate file with CSS variables
- [ ] All JavaScript in separate file with event listeners
- [ ] No inline event handlers in HTML
- [ ] All hardcoded content moved to data structure
- [ ] Keyboard accessibility for all interactive elements
- [ ] Movie poster hover cards working without scroll issues
- [ ] Page load time maintained or improved
- [ ] Zero accessibility warnings in browser tools

---

## Notes

- Maintain backwards compatibility with existing URLs
- Keep the minimal aesthetic - refactoring should be invisible to users
- Test on multiple browsers (Chrome, Firefox, Safari)
- Test on mobile devices after each phase
