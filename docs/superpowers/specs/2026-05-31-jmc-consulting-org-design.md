# JMC Consulting (thecharteddefense.org) — Design Spec

**Date:** 2026-05-31
**Owner:** James Michael Coleman, MD (JMC Consulting, LLC)
**Goal:** Give `thecharteddefense.org` its own identity as the marketing site for **JMC Consulting, LLC**, a medical-legal consulting practice — separate from the existing `thecharteddefense.com` educational brand.

---

## 1. Background / Current State

- `thecharteddefense.com` — existing single-page static site. Source at
  `…/01-Work/02-The-Charted-Defense/website/`, GitHub repo `mikecuab/thecharteddefense-website`,
  hosted on **Netlify** (auto-deploys on push), DNS on **Cloudflare**.
- `thecharteddefense.org` — currently **redirects to `.com`** via Cloudflare. Both domains live in the
  same Cloudflare account; `.org` mailbox is `michaelcoleman@thecharteddefense.org` (Google).
- This project replaces the redirect with a standalone JMC Consulting site.

## 2. Goals

1. A professional one-page site for JMC Consulting, LLC at `thecharteddefense.org`.
2. A **Google Calendar appointment** booking link as the primary call-to-action.
3. An **About** section establishing Michael's credibility (MD, hospital-medicine leadership, peer-review
   chair, JD-Next coursework).
4. Brand-consistent styling using the supplied JMC assets (navy caduceus-and-scales logo, serif headlines).
5. Leave `thecharteddefense.com` and its repo behavior untouched.

## 3. Non-Goals (YAGNI)

- No CMS, blog, or multi-page navigation — a single scrolling page only.
- No contact-form backend (booking happens via Google Calendar; email shown as `mailto:`).
- No e-commerce, client portal, or payments.
- No changes to `.com` content or its Netlify site.

## 4. Architecture / Hosting

- **One repo, two Netlify sites.** Add a new subfolder `jmc/` to the existing
  `thecharteddefense-website` repo. It is fully self-contained (HTML/CSS/images).
- **Netlify site B** (new) is created from the same GitHub repo with **base directory = `jmc`**.
  A `jmc/netlify.toml` sets `publish = "."` plus the same security headers as `.com`.
- The existing **Netlify site A** (`.com`, publish `.` at repo root) is unaffected — base-directory
  isolation keeps the two builds from colliding.
- **Cloudflare DNS:** remove the `.org → .com` redirect (Bulk Redirect / Page Rule) and point
  `thecharteddefense.org` (apex + `www`) at Netlify site B per Netlify's custom-domain instructions.
- Deploy is `git push` → Netlify builds both sites; only `jmc/**` affects site B.

**Manual steps requiring Michael's accounts** (guided, one-time):
1. Netlify: "Add new site" → import the existing repo → set base directory `jmc` → assign domain.
2. Cloudflare: delete the `.org` redirect rule; add/confirm DNS records Netlify provides.

## 5. Page Structure (single scroll, anchor nav)

| Section | Anchor | Content |
|---|---|---|
| Sticky nav | — | JMC logo + links (Services · Case Reviews · About Mike Coleman · Contact) + "Schedule a Consultation" button |
| Hero | `#top` | Headline "Bridging Clinical Leadership with Definitive Medical-Legal Analysis." · 13-years subhead · "Schedule a Preliminary Consultation" CTA · navy-suit headshot |
| Services | `#services` | 3 cards: **Standard of Care Review**, **Causation & Damages**, **Expert Review & Testimony** |
| Case Reviews | `#reviews` | Positioning: preliminary candid review first; differentiator = chairs hospital peer review / defensibility assessments |
| About Mike Coleman | `#about` | Bio from résumé + credentials block (ABFM board cert, Focused Practice in Hospital Medicine, Peer Review chair, AL license/DEA, MD UAB, JD-Next) |
| Contact / Schedule | `#contact` | Google Calendar booking (embed + button) · business email `michaelcoleman@thecharteddefense.org` |
| Footer | — | JMC Consulting, LLC · Madison, AL · informational/not-legal-advice disclaimer |

## 6. Brand / Styling

- **Palette:** deep navy `#15294d`, ink `#1a1f29`, muted `#5b6573`, warm off-white bg `#f4f3f0`,
  panel white, thin warm gold accent `#c8b273` for eyebrow labels.
- **Type:** serif display (Playfair Display, Georgia fallback) for headlines; humanist sans
  (Source Sans 3, system fallback) for body. Google Fonts with local fallbacks.
- **Assets** (already on disk):
  - Logo mark: `~/Downloads/Gemini_Generated_Image_fki76nfki76nfki7.png`
  - Hero/headshot source: `~/Downloads/Gemini_Generated_Image_ei89u4ei89u4ei89.png`
    (crop the navy-suit headshot for the hero; rebuild hero text in HTML for responsiveness/SEO).
  - Both copied into `jmc/images/` with descriptive names.
- Responsive: hero and grids collapse to single column on mobile.

## 7. The Booking Link

- Primary CTA and Contact section link to Michael's **Google Calendar appointment schedule**
  (`https://calendar.app.google/…`) created on the `michaelcoleman@thecharteddefense.org` account.
- If the live link is not yet available at build time, use a clearly-marked placeholder `href="#contact"`
  and a TODO so it can be swapped in one edit.

## 8. Files Produced

```
jmc/
  index.html         # the one-page site
  netlify.toml        # publish ".", security headers
  images/
    jmc-logo.png
    michael-coleman-consulting.png   # cropped navy-suit headshot
```

## 9. Success Criteria

- `jmc/index.html` opens locally and renders the full page with real content and assets.
- Booking CTA points to the Google Calendar link (or marked placeholder).
- Mobile layout is clean (single column, tappable CTA).
- After Netlify + Cloudflare wiring, `https://thecharteddefense.org` serves the JMC site and no longer
  redirects to `.com`; `.com` is unchanged.

## 10. Open Items

- Live Google Calendar booking URL (Michael to supply; placeholder otherwise).
- Confirm apex-vs-www handling in Cloudflare during the wiring step.
