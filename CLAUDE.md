# CSS Design Guidelines

Practical CSS rules for this project. Focus on what works, avoid over-engineering.

## Core Principles

1. **Use what exists** - Don't add new CSS variables unless absolutely necessary
2. **Follow BEM** - Use `block__element--modifier` naming consistently  
3. **Start with defaults** - Use `--spacing-md`, `--color-text`, `--font-size-base` first
4. **Copy working patterns** - See design-system.html for proven combinations

## Quick Decision Guide

### What CSS Variable Should I Use?

**For Spacing:**
- Between page sections? → `--spacing-xxl` (48px)
- Between components? → `--spacing-xl` (32px)  
- Inside components? → `--spacing-md` (16px) ← **Start here**
- Button padding? → `--spacing-sm` (8px)
- Icon to text? → `--spacing-xs` (4px)

**For Colors:**
- Main text? → `--color-text` ← **Start here**
- Secondary text? → `--color-text-light`
- Interactive elements? → `--color-primary`
- Highlights? → `--color-accent`

**For Typography:**
- Page titles? → `--font-size-xxl` (32px)
- Section headings? → `--font-size-xl` (24px)
- Card titles? → `--font-size-large` (18px)
- Body text? → `--font-size-base` (16px) ← **Start here**
- Captions? → `--font-size-small` (14px)

**For Shadows:**
- Cards? → `--shadow-md` ← **Start here**
- Dropdowns? → `--shadow-lg`  
- Modals? → `--shadow-xl`
- Hover effects? → `--shadow-sm`

## BEM Rules (Simple Version)

### Class Naming: `block__element--modifier`

**Good Examples:**
- `.article-card` (block)
- `.article-card__title` (element)  
- `.article-card--featured` (modifier)

**Bad Examples:**
- `.article-card__header__title` (too deep - max 1 level)
- `.articleCard` (use kebab-case, not camelCase)

### When to Split a Component

**Keep together if:**
- Everything serves one clear purpose
- You can explain it in one sentence
- < 10 elements total

**Split if:**
- Component does multiple things
- Elements could be reused elsewhere
- Hard to remember what's inside

### File Organization

```
css/
├── base.css (variables only)
├── blocks/
│   ├── article-card.css
│   ├── nav-tabs.css
│   └── button.css
└── pages/
    ├── home.css (page-specific overrides)
    └── article.css
```

## Common Patterns (Copy These)

### Standard Button
```css
.button {
  padding: var(--spacing-sm) var(--spacing-md);
  background: var(--color-primary);
  color: white;
  border-radius: var(--border-radius);
  border: none;
  cursor: pointer;
}

.button:hover {
  background: var(--color-primary-hover);
}
```

### Card Component  
```css
.card {
  padding: var(--spacing-md);
  background: var(--color-background);
  border-radius: var(--border-radius);
  box-shadow: var(--shadow-md);
}

.card__title {
  font-size: var(--font-size-large);
  color: var(--color-text);
  margin-bottom: var(--spacing-sm);
}
```

### Text Hierarchy
```css
h1 { font-size: var(--font-size-xxl); }  /* Page titles */
h2 { font-size: var(--font-size-xl); }   /* Section titles */
h3 { font-size: var(--font-size-large); } /* Card titles */
p { font-size: var(--font-size-base); }   /* Body text */
small { font-size: var(--font-size-small); } /* Captions */
```

## Scope of Influence Guidelines

### The Problem: Unclear Impact
When CSS has unclear scope, developers can't predict what will be affected by changes. This leads to broken layouts and fear of modifying styles.

### Core Rule: Make Scope Obvious
Every CSS rule should make it clear **exactly** what elements it will affect.

### Tag Selectors: Usually Avoid
```css
/* Bad - affects ALL h1 elements everywhere */
h1 {
  font-size: 32px;
  color: red;
}

/* Good - affects only article titles */
.article__title {
  font-size: var(--font-size-xxl);
  color: var(--color-text);
}
```

**Exception:** Tag selectors are OK for primitive/reset styles:
```css
/* OK - basic reset/primitive styles */
* { box-sizing: border-box; }
body { font-family: var(--font-family-primary); }
img { max-width: 100%; }
```

### Class Selectors: Preferred
```css
/* Good - clear scope */
.hero-illustration__person::before {
  content: '👩‍💻';
  font-size: 80px;
}

/* Good - modifier affects specific state */
.nav-tabs__item--active {
  background: var(--color-primary);
  color: white;
}
```

### Descendant Selectors: Use Carefully
```css
/* Risky - could affect nested components */
.article p {
  margin-bottom: var(--spacing-md);
}

/* Better - explicit about what you're targeting */
.article__content p {
  margin-bottom: var(--spacing-md);
}

/* Best - use BEM classes */
.article__paragraph {
  margin-bottom: var(--spacing-md);
}
```

### Page Overrides: Minimal and Explicit
```css
/* Bad - too broad scope */
.p-about h1 { font-size: 40px; }

/* Good - specific and contained */
.p-about__hero-title {
  font-size: var(--font-size-xxl);
}

/* OK - clearly scoped override */
.p-design-system .article-card {
  margin-bottom: var(--spacing-xl);
}
```

### Decision Framework
Before writing CSS, ask:
1. **What exactly will this affect?** If you can't answer precisely, use more specific selectors.
2. **Will this break if HTML structure changes?** If yes, use BEM classes instead.
3. **Could this accidentally style other components?** If yes, increase specificity.
4. **Can another developer understand the scope?** If no, add comments or use clearer selectors.

## What NOT to Do

### Don't Create New Variables
```css
/* Bad - creating new variables */
:root {
  --my-special-spacing: 13px;
  --custom-blue: #1234ff;
}

/* Good - use existing ones */
.my-component {
  padding: var(--spacing-md);
  color: var(--color-primary);
}
```

### Don't Go Too Deep with BEM
```html
<!-- Bad - too nested -->
<div class="card__header__title__icon--small">

<!-- Good - extract or flatten -->
<div class="card__title-icon card__title-icon--small">
<!-- or -->
<div class="title-icon title-icon--small">
```

### Don't Make Pages Override Everything
```css
/* Bad - page styles doing too much */
.p-home .card { /* completely different card */ }
.p-home .button { /* completely different button */ }

/* Good - page styles for layout only */
.p-home { /* page layout */ }
.p-home__hero { /* page-specific sections */ }
```

### Don't Write CSS with Unclear Scope
```css
/* Bad - unclear what this affects */
h1 { font-size: 40px; color: blue; }
p { margin: 20px; }
.content div { padding: 10px; }

/* Good - explicit scope */
.page-title { font-size: var(--font-size-xxl); }
.article__paragraph { margin-bottom: var(--spacing-md); }
.sidebar__widget { padding: var(--spacing-sm); }
```

### Don't Use Overly Complex Selectors
```css
/* Bad - fragile and unclear */
.main .content .sidebar ul li a:not(.active) {
  color: var(--color-text-light);
}

/* Good - simple and clear */
.sidebar__link {
  color: var(--color-text-light);
}

.sidebar__link--active {
  color: var(--color-primary);
}
```

## Development Commands

```bash
# Development server
npm run dev

# Build for production  
npm run build
```

## Available CSS Variables (30 total)

### Colors (14)
- `--color-primary` #4a9eff
- `--color-primary-hover` #3b87d9
- `--color-accent` #ffb347
- `--color-text` #333
- `--color-text-light` #666
- `--color-text-lighter` #999
- `--color-background` #ffffff
- `--color-background-light` #f5f5f5
- `--color-background-lighter` #fafafa
- `--color-border` #e0e0e0
- `--color-border-light` #f0f0f0

### Typography (6)
- `--font-family-primary` (system fonts)
- `--font-size-base` 16px
- `--font-size-small` 14px
- `--font-size-large` 18px
- `--font-size-xl` 24px
- `--font-size-xxl` 32px

### Spacing (6)
- `--spacing-xs` 4px
- `--spacing-sm` 8px  
- `--spacing-md` 16px ← **Default**
- `--spacing-lg` 24px
- `--spacing-xl` 32px
- `--spacing-xxl` 48px

### Shadows (4)
- `--shadow-sm` (hover effects)
- `--shadow-md` (cards) ← **Default**
- `--shadow-lg` (dropdowns)
- `--shadow-xl` (modals)

### Other
- `--border-radius` 8px
- `--border-radius-sm` 4px
- `--border-radius-lg` 12px
- `--transition-fast` 150ms ease-in-out

That's it! Keep it simple.