---
name: landing-page-architect
description: >
  Build high-converting landing pages with persuasive copywriting, CRO best
  practices, and production-ready HTML/CSS/JS. Use this skill whenever the user
  asks to create a landing page, sales page, lead capture page, product page,
  waitlist page, or any single-page marketing asset — even if they only describe
  an offer, audience, or goal without explicitly saying "landing page". Triggers
  on phrases like "quero uma página de vendas", "cria uma LP", "página de
  captura", "página de produto", "landing page para…", or when the user describes
  a product/service and wants to promote or sell it online. Always use this skill
  before writing any landing page markup or copy.
compatibility:
  models: [claude-opus-4, claude-opus-3-7, claude-opus-3-5]
  context: cursor, claude-code, claude-ai
version: "1.0.0"
---

# Landing Page Architect

Build high-converting, psychologically-optimized landing pages as
**single-file, production-ready HTML** with embedded CSS and minimal JS.
Prioritize conversion rate above aesthetics, clarity above cleverness.

---

## Phase 0 — Gather Context (before writing any code)

Ask the user for the following if not already provided. Collect all answers in
one shot; do not ask one question at a time.

| # | Question | Why it matters |
|---|----------|----------------|
| 1 | What is the product / service / offer? | Core value proposition |
| 2 | Who is the primary audience? Pain points? | Empathy copy, language match |
| 3 | What is the ONE action the visitor must take? | CTA design |
| 4 | What proof exists? (testimonials, metrics, logos, case studies) | Social proof sections |
| 5 | Is there a real deadline, limited seats, or limited stock? | Urgency triggers |
| 6 | What is the price point / offer structure? | Anchor pricing, value stack |
| 7 | Any brand colors, fonts, or tone guidelines? | Design system seed |
| 8 | Which analytics/tracking pixels are needed? (GA4, GTM, Meta Pixel) | Tag placement |
| 9 | Where does the form submit? (webhook URL, email, CRM) | Form action |

If the user says "just build it" or provides a brief, infer reasonable defaults
and state assumptions clearly at the top of your response.

---

## Phase 1 — Strategy Brief (output before writing code)

Before the first line of HTML, output a compact brief:

```
STRATEGY BRIEF
──────────────
Audience      : [who they are + core pain]
Promise       : [headline value proposition in one sentence]
Primary CTA   : [exact button label + action]
Urgency lever : [real scarcity or manufactured? which type?]
Proof type    : [testimonials / numbers / logos / case study]
Tone          : [e.g. authoritative, empathetic, urgent, playful]
Design anchor : [color palette seed + font pairing decision]
```

---

## Phase 2 — Page Architecture

Build sections in this exact order. Never skip sections; collapse into minimal
variants if content is unavailable.

### 1. HERO
- **Headline**: Outcome-focused, ≤ 10 words. Speak the transformation, not the
  tool. Use the user's own language.
- **Subheadline**: One sentence expanding the promise. Address the primary pain.
- **Primary CTA button**: High-contrast, action verb + benefit
  (e.g. "Start My Free Trial", "Get Instant Access").
- **Trust micro-copy** directly below CTA: e.g. "No credit card required · Cancel
  anytime · 2,400+ customers"
- **Hero visual**: Describe what image/illustration to use; output a placeholder
  `<div>` with alt text and aspect-ratio if no image URL is provided.

### 2. PROBLEM AWARENESS
- 3 short paragraphs or a 3-column icon grid.
- Mirror the user's internal monologue. Use "You know that feeling when…"
  structure.
- End with a pivot sentence: "There's a better way."

### 3. SOLUTION BRIDGE
- One-paragraph narrative: product as the bridge from pain → outcome.
- Avoid feature lists here; focus on the transformation.

### 4. BENEFITS (Outcome-focused)
- 4–6 bullets. Format: **Bold outcome** — supporting detail sentence.
- Lead with the most emotionally resonant benefit.
- No feature names; translate every feature into a user result.

### 5. SOCIAL PROOF — Block A
- 3 testimonials minimum. Each must contain:
  - Verbatim quote (or placeholder in `[BRACKETS]`)
  - Name, title, company (real or placeholder)
  - Star rating (⭐⭐⭐⭐⭐ or SVG stars)
  - Optional avatar placeholder
- Position immediately after benefits to validate the promise just made.

### 6. HOW IT WORKS
- Exactly 3 steps. Numbered. Icon + title + one-sentence description.
- Use active verbs: "Connect", "Configure", "Launch" — not "Step 1: …"
- Optional: animated progress indicator (CSS-only).

### 7. OFFER DETAILS / VALUE STACK
- List everything included. Use ✓ checkmarks.
- Show total perceived value with strikethrough prices if applicable:
  `~~R$1.200~~ Incluído gratuitamente`
- Anchor the price visually against the value stack.

### 8. SOCIAL PROOF — Block B (near CTA)
- 1 featured case study or standout metric:
  `"We increased conversions by 312% in 30 days" — [Name, Company]`
- Or a logo strip if B2B: "Trusted by teams at [Logo] [Logo] [Logo]"

### 9. OBJECTION HANDLING / FAQ
- 5–7 questions. Use `<details><summary>` accordion pattern.
- Answer the real objection, not the surface question.
- Final FAQ entry must be a risk-reversal guarantee.

### 10. GUARANTEE BLOCK
- Prominent badge or callout box.
- Specific: "30-day money-back guarantee, no questions asked."
- Include a trust badge icon (SVG inline).

### 11. FINAL CTA SECTION
- Restate the core promise in 1 sentence.
- Repeat the primary CTA button (identical label, same style).
- Add urgency trigger: countdown timer (JS) or scarcity copy.
- Repeat trust micro-copy from hero.

---

## Phase 3 — Behavioral Psychology Checklist

Apply every trigger. Mark each as implemented in a comment block at end of file.

| Trigger | Implementation |
|---------|----------------|
| **Loss aversion** | "Don't miss out", limited offer framing |
| **Social proof** | Testimonials, logos, user counts |
| **Authority** | Expert credentials, media mentions, certifications |
| **Scarcity** | Limited seats / stock / time |
| **Urgency** | Countdown timer or deadline copy |
| **Commitment** | Low-friction first step ("Free", "No CC required") |
| **Clarity bias** | One CTA per section; no navigation links |
| **Cognitive fluency** | Short sentences, bullet lists, scannable headers |

---

## Phase 4 — UX & Design System Rules

### Typography
- Display font: distinctive, character-rich (e.g. Fraunces, Instrument Serif,
  Syne, Familjen Grotesk). Never Inter, Roboto, or Arial.
- Body font: high-readability companion (e.g. DM Sans, Plus Jakarta Sans,
  Outfit).
- Scale: `--text-xs` 12px → `--text-6xl` 72px (8-step scale, 1.25 ratio).
- Line height: 1.15 for headlines, 1.65 for body.

### Color System (CSS custom properties)
```css
:root {
  --color-brand:   /* primary — high saturation */;
  --color-accent:  /* CTA — maximum contrast against brand */;
  --color-bg:      /* page background */;
  --color-surface: /* card / section background */;
  --color-text:    /* body text — min 4.5:1 contrast ratio */;
  --color-muted:   /* secondary text */;
  --color-border:  /* subtle dividers */;
}
```

### Spacing Scale
Use `--space-{n}` tokens: 4, 8, 12, 16, 24, 32, 48, 64, 96, 128px.

### CTA Button Standards
- Background: `var(--color-accent)`
- Padding: `16px 40px` desktop, `14px 28px` mobile
- Border-radius: 8px (rounded but not pill unless brand demands it)
- Font-weight: 700
- Text-transform: none (sentence case converts better than ALL CAPS)
- Hover: `brightness(1.1)` + `translateY(-2px)` + box-shadow
- Transition: `all 0.2s ease`
- Min tap target: 44×44px

### Layout
- Mobile-first. Breakpoints: 375px → 768px → 1280px.
- Max content width: `1100px`, centered with `margin: 0 auto`.
- Section vertical padding: `clamp(64px, 10vw, 128px)`.
- No horizontal scrolling on any viewport.

---

## Phase 5 — Performance Requirements

- Zero external CSS frameworks (no Bootstrap, no Tailwind CDN bloat).
- Inline all critical CSS in `<style>` inside `<head>`.
- Defer all non-critical JS with `defer` or `type="module"`.
- Use `loading="lazy"` on all images below the fold.
- Use `<picture>` + `webp` with `jpg` fallback for hero images.
- Font loading: `font-display: swap`; load max 2 font families.
- No jQuery. Vanilla JS only.
- Cumulative Layout Shift (CLS) < 0.1: set explicit `width` and `height` on all
  media elements.

---

## Phase 6 — Analytics & Tracking Template

Insert the following placeholder blocks in the correct positions:

```html
<!-- HEAD: Google Tag Manager -->
<!-- Replace GTM-XXXXXXX with real container ID -->
<script>(function(w,d,s,l,i){...})(window,document,'script','dataLayer','GTM-XXXXXXX');</script>

<!-- HEAD: Meta Pixel -->
<script>!function(f,b,e,v,n,t,s){...}('YOUR_PIXEL_ID');</script>

<!-- BODY open: GTM noscript fallback -->
<noscript><iframe src="https://www.googletagmanager.com/ns.html?id=GTM-XXXXXXX"...></iframe></noscript>

<!-- FORM: conversion event on submit -->
<script>
  document.getElementById('lead-form').addEventListener('submit', () => {
    gtag('event', 'generate_lead', { event_category: 'form' });
    fbq('track', 'Lead');
  });
</script>
```

---

## Phase 7 — Form Engineering

- Minimum fields: name + email only (default). Add phone only if explicitly
  required.
- `autocomplete` attributes on every input.
- Real-time inline validation (no alert boxes).
- Submit button: show spinner on click, disable to prevent double-submit.
- Success state: replace form with confirmation message inline (no redirect
  unless required).
- Error state: field-level error copy in red below the input.
- Submission target: use `fetch()` POST to `action` URL or webhook.

```html
<form id="lead-form" novalidate>
  <input type="text"  name="name"  autocomplete="given-name"  required>
  <input type="email" name="email" autocomplete="email"        required>
  <button type="submit" id="cta-submit">
    <span class="btn-label">Get Instant Access</span>
    <span class="btn-spinner" aria-hidden="true" hidden>⟳</span>
  </button>
  <p class="form-microcopy">🔒 Your data is safe. We never spam.</p>
</form>
```

---

## Phase 8 — Sticky CTA Bar (optional, recommended for long pages)

```html
<div id="sticky-cta" class="sticky-bar" aria-label="Call to action bar">
  <p class="sticky-bar__copy"><!-- restate core benefit in 6 words --></p>
  <a href="#lead-form" class="btn btn--accent">Get Started Now</a>
</div>
```

Show after 600px scroll. Hide when primary hero CTA is visible (use
`IntersectionObserver`). CSS position: `fixed; bottom: 0; width: 100%`.

---

## Phase 9 — Countdown Timer (urgency)

```js
function startCountdown(targetDate) {
  const el = document.getElementById('countdown');
  const tick = () => {
    const diff = targetDate - Date.now();
    if (diff <= 0) { el.textContent = 'Offer expired'; return; }
    const h = Math.floor(diff / 3.6e6);
    const m = Math.floor((diff % 3.6e6) / 60000);
    const s = Math.floor((diff % 60000) / 1000);
    el.textContent = `${h}h ${m}m ${s}s remaining`;
    setTimeout(tick, 1000);
  };
  tick();
}
// Usage: startCountdown(new Date('2025-12-31T23:59:59'));
```

If no real deadline exists, use a session-based timer (expires when tab closes).
Never fake a permanent countdown that resets — it destroys trust.

---

## Phase 10 — SEO Baseline

```html
<title>[Primary Keyword] — [Benefit Statement] | [Brand]</title>
<meta name="description" content="[130–160 chars: who it's for + core benefit + CTA]">
<link rel="canonical" href="https://yourdomain.com/landing-page-slug">
<meta property="og:title"       content="...">
<meta property="og:description" content="...">
<meta property="og:image"       content="https://...1200x630.jpg">
<meta property="og:type"        content="website">
<script type="application/ld+json">
  { "@context": "https://schema.org", "@type": "Product", ... }
</script>
```

---

## Output Format

Deliver the final result in this order:

1. **Strategy Brief** (plain text block, see Phase 1)
2. **Complete single-file HTML** — all CSS in `<style>`, all JS in `<script
   defer>` before `</body>`, no external dependencies except Google Fonts `@import`.
3. **CRO Notes** — 3–5 bullet points of specific optimizations applied and why.
4. **A/B Test Suggestions** — 2–3 hypotheses to test (headline variants, CTA
   copy, hero image).

---

## Quality Gate

Before returning output, verify:

- [ ] One and only one primary CTA per above-the-fold viewport
- [ ] No navigation links (they leak conversions)
- [ ] CTA button appears in Hero, after Benefits, after FAQ, and in Final section
- [ ] All images have `alt` text and `width`/`height` attributes
- [ ] Color contrast ≥ 4.5:1 for body text, ≥ 3:1 for large text
- [ ] Page renders correctly at 375px, 768px, and 1280px
- [ ] Zero `console.error` from inline JS
- [ ] `<html lang="pt-BR">` (or correct locale) is set
- [ ] Behavioral psychology checklist comment block present at end of file