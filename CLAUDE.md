# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

Redesign of the website for the Department of Financial Monitoring (ДФМ) under the National Bank of Tajikistan — a Financial Intelligence Unit (FIU/ПФР). Covers AML/CFT/WMD-proliferation (ПОД/ФТ/ФРОМП). Static single-page site, no build system, no framework.

Three files do everything:

- [index.html](index.html) — full page markup, inline SVGs, `data-i18n` attributes on all user-visible strings
- [styles.css](styles.css) — design system tokens + all component styles (no preprocessor)
- [app.js](app.js) — i18n, accessibility toggle, mobile drawer, scroll-reveal animations

Reference documents in [assets/](assets/):
- [TASK.md](assets/TASK.md) — analytical brief, site IA, design system spec
- [PROMPT.md](assets/PROMPT.md) — original design prompt with full requirements

## Running Locally

```bash
python3 -m http.server 8080
# or
npx serve .
```

No install, no build, no compilation. Open `index.html` directly in a browser also works.

## Target Audiences (drives all navigation/IA decisions)

1. **Субъекты финмониторинга** — banks, NGOs, insurers (primary audience; key section)
2. **Граждане и бизнес** — citizens reporting suspicious transactions
3. **Партнёры** — government bodies, foreign FIUs, international orgs (FATF, Egmont)

## Page Structure (top → bottom)

1. Topbar — lang switcher (ТҶ/РУ/EN), accessibility toggle, NBT legal portal link
2. Masthead — logo, global search, CTA «Личный кабинет субъекта»
3. Nav — О Департаменте · Субъектам финмониторинга · Правовая база · Противодействие · Международное сотрудничество · Пресс-центр · Сервисы
4. Hero — dark navy bg + heraldic pattern, mission headline, 2 CTAs
5. Audience cards — 3 scenario cards («Я субъект» / «Я гражданин» / «Я партнёр»)
6. Directions — ПОД / ФТ / ФРОМП icon cards
7. Registers — alert-styled block linking to sanctions lists and subject registry
8. News — 3–4 minimal cards
9. Legal basis — key laws + FATF 40 Recommendations
10. International — FATF / EAG / Egmont logos
11. Footer — dark navy, contacts, hotline, social links, NBT link, copyright

## Design System

### Color Tokens

```css
--dmm-navy-900: #0A2540   /* header/footer/dark sections */
--dmm-navy-800: #103155   /* section backgrounds */
--dmm-blue-600: #1E5BA8   /* buttons, links, brand */
--dmm-blue-500: #2D6FCB   /* hover/active */
--dmm-blue-100: #E6EEF7   /* card light backgrounds */
--dmm-teal-600: #0E8A7D   /* DMM accent, icons */
--dmm-alert-600: #C2410C  /* sanctions/terrorist lists ONLY */
--dmm-gold-500: #C9A227   /* heraldic accent (crest/ornament) */
--dmm-ink-900: #14202E    /* body text */
--dmm-ink-600: #475569    /* secondary text */
--dmm-line-200: #E2E8F0   /* borders */
--dmm-bg-50: #F7F9FC      /* page background */
```

Ratio rule: ~70% neutrals / ~20% blue / ~10% accents. `--dmm-alert-600` only for sanctions lists and critical warnings, never decorative.

### Typography

- **Headings:** Manrope → PT Root UI → Inter → system-ui (`--font-head`)
- **Body:** Inter → PT Sans → system-ui (`--font-body`)
- All fonts **must** render Tajik Cyrillic correctly: ҷ ӣ ӯ ҳ ғ қ
- Hero: `clamp(2.2rem, 4vw, 3.5rem)`, weight 700, line-height 1.1
- Body line-height 1.6 (legal texts are long)

### Component Rules

- Card radius: `10px`; button radius: `8px` — solid, not playful
- Shadows: `0 1px 3px rgba(10,37,64,.08)` (soft, low)
- Icons: inline SVG, linear style, 1.5–1.8px stroke (no icon library)
- Grid: 12-col, `max-width: 1200px`, spacing multiples of 8px
- All interactive elements need visible `hover` + `focus` states

## Architecture

### i18n

All strings in `I18N` object at top of [app.js](app.js). Locales: `ru` (default/fallback), `tg`, `en`.

- Text nodes: `data-i18n="key"` on element
- Placeholders: `data-i18n-ph="key"` on input
- Persisted to `localStorage` key `dmm_lang`
- `applyLang(lang)` swaps all annotated nodes atomically

When adding content: add keys to **all three locales** in `I18N`.

### Accessibility Mode

`data-a11y-toggle` button toggles `.a11y` class on `<html>`. CSS `.a11y` selectors apply: larger font, grayscale, disabled background images. State persisted under `dmm_a11y`. WCAG AA minimum (4.5:1 contrast).

### Mobile Drawer

`.drawer` / `.drawer-backdrop` via `initDrawer()`. Opens: `.nav-toggle` click. Closes: backdrop click, close button, Escape, any drawer `<a>` click.

### Scroll Reveal

Class `reveal` on elements + IntersectionObserver in `initReveal()`. Add `data-delay="N"` (ms) for stagger. Graceful fallback: adds `.in` immediately if `IntersectionObserver` unavailable.

## Conventions

- No stock photos, no "playful" elements — government tone
- SVG icons inline in HTML (no external icon library)
- New section: markup in `index.html` + strings in all 3 locales in `I18N` + styles in `styles.css`
- `assets/` — committed static assets; `uploads/` — user-uploaded content
