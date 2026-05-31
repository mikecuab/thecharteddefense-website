# JMC Consulting (.org) Site — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a one-page JMC Consulting, LLC marketing site in a new `jmc/` subfolder of the `thecharteddefense-website` repo, ready to deploy to `thecharteddefense.org` as a second Netlify site.

**Architecture:** Self-contained static page (`jmc/index.html` + `jmc/images/` + `jmc/netlify.toml`). Hand-written HTML/CSS, no build step, no JS framework. A second Netlify site builds from base directory `jmc`; `.com` site is untouched.

**Tech Stack:** Static HTML5 + CSS (Google Fonts: Playfair Display, Source Sans 3, with system fallbacks). macOS `sips` for image cropping. Python `http.server` for local preview. Netlify (hosting) + Cloudflare (DNS) for deploy.

**Repo root (all paths relative to it):** `~/Library/Mobile Documents/com~apple~CloudDocs/01-Work/02-The-Charted-Defense/website/`

**Verification method:** Each build task is verified by rendering the page (Launch preview / local `http.server`) and visually confirming specific elements, then committing. "Test = render + check" for this static site.

---

## File Structure

```
jmc/
  index.html        # the entire one-page site (semantic sections + inline <style>)
  netlify.toml       # publish ".", security headers (mirrors .com)
  images/
    jmc-logo.png                    # JMC caduceus+scales logo mark
    michael-coleman-consulting.png   # navy-suit headshot, cropped from hero source
```

Source assets (already on disk):
- Logo: `~/Downloads/Gemini_Generated_Image_fki76nfki76nfki7.png`
- Hero/headshot source: `~/Downloads/Gemini_Generated_Image_ei89u4ei89u4ei89.png`

Booking link: `https://calendar.app.google/hVWEfvB3Ns5hUcqf7`

---

### Task 1: Scaffold the `jmc/` folder and stage assets

**Files:**
- Create dir: `jmc/`, `jmc/images/`
- Copy: logo + hero source into `jmc/images/`

- [ ] **Step 1: Create folders**

```bash
cd ~/Library/Mobile\ Documents/com~apple~CloudDocs/01-Work/02-The-Charted-Defense/website
mkdir -p jmc/images
```

- [ ] **Step 2: Copy the logo mark into the site**

```bash
cp ~/Downloads/Gemini_Generated_Image_fki76nfki76nfki7.png jmc/images/jmc-logo.png
```

- [ ] **Step 3: Inspect hero source dimensions (to plan the crop)**

```bash
sips -g pixelWidth -g pixelHeight ~/Downloads/Gemini_Generated_Image_ei89u4ei89u4ei89.png
```
Expected: prints width/height (≈450×600-class portrait). Note the values for Step 4.

- [ ] **Step 4: Crop the navy-suit headshot from the right portion of the hero**

The subject occupies roughly the right ~55% of the composite. Crop to a portrait focused on
head + shoulders + portfolio. Using `sips` (adjust offsets to the values from Step 3):

```bash
# Copy first, then crop the copy so the original in Downloads is preserved
cp ~/Downloads/Gemini_Generated_Image_ei89u4ei89u4ei89.png jmc/images/michael-coleman-consulting.png
# Crop to the right ~55% width, full height (tune numbers after viewing Step 3 output)
sips --cropOffset 0 200 --cropToHeightWidth 600 250 jmc/images/michael-coleman-consulting.png
```
NOTE: `sips --cropToHeightWidth H W` then `--cropOffset top left`. If the framing is off after
rendering in Task 4, re-copy from the original and re-crop — do not iterate on an already-cropped file.

- [ ] **Step 5: Verify assets exist and are valid images**

```bash
sips -g pixelWidth -g pixelHeight jmc/images/jmc-logo.png jmc/images/michael-coleman-consulting.png
```
Expected: both report valid dimensions (no error).

- [ ] **Step 6: Commit**

```bash
git add jmc/images
git commit -m "jmc: stage logo and headshot assets"
```

---

### Task 2: Build `jmc/index.html` — document shell, nav, hero

**Files:**
- Create: `jmc/index.html`

- [ ] **Step 1: Write the document head + brand styles + sticky nav + hero**

Create `jmc/index.html` with the `<!DOCTYPE html>` skeleton, `<meta viewport>`, SEO `<title>`
and `<meta name="description">`, Google Fonts links, the full `<style>` block (palette + type from
spec §6), and the first two `<body>` sections. Use the validated mockup
`/Users/jamescoleman/jmc-brainstorm/.superpowers/brainstorm/87878-1780237144/content/jmc-landing-v2.html`
as the styling/markup baseline, with these changes:
- Replace the inline SVG logo in the nav with `<img src="images/jmc-logo.png" alt="JMC Consulting, LLC">` sized to ~38px tall.
- Replace the hero `.shot` placeholder div with `<img src="images/michael-coleman-consulting.png" alt="Michael Coleman, MD">`.
- Remove the orange `.banner` preview strip (that was companion-only).
- Make nav `.links` real anchor links: `<a href="#services">Services</a>` etc.
- Make both hero CTA buttons real links to the booking URL:
  `<a class="btn-primary" href="https://calendar.app.google/hVWEfvB3Ns5hUcqf7" target="_blank" rel="noopener">Schedule a Preliminary Consultation →</a>`
- `<title>JMC Consulting, LLC — Medical-Legal Consulting | Michael Coleman, MD</title>`
- `<meta name="description" content="Medical-legal consulting by Michael Coleman, MD — standard of care, causation, and damages analysis in malpractice litigation. 13 years of hospital medicine experience.">`

- [ ] **Step 2: Render and verify nav + hero**

```bash
cd ~/Library/Mobile\ Documents/com~apple~CloudDocs/01-Work/02-The-Charted-Defense/website/jmc
python3 -m http.server 8765 >/dev/null 2>&1 &
echo "open http://localhost:8765"
```
Open `http://localhost:8765` (Launch preview / browser). Confirm: logo image renders in nav,
nav links present, hero headline in serif, subhead, headshot image visible on the right, CTA button
navy. Leave the server running for later tasks (stop with `kill %1` at the end).

- [ ] **Step 3: Commit**

```bash
git add jmc/index.html
git commit -m "jmc: add document shell, nav, and hero"
```

---

### Task 3: Add Services, Case Reviews, and About sections

**Files:**
- Modify: `jmc/index.html` (append sections before the contact section)

- [ ] **Step 1: Add the three sections**

Append into `<body>`: the `#services` section (3 cards — Standard of Care Review · Causation &
Damages · Expert Review & Testimony), the `#reviews` Case Reviews positioning section, and the
`#about` section with the résumé-based bio + the 6-item credentials grid, exactly as in the v2 mockup
(spec §5 and §6). Keep the `images/michael-coleman-consulting.png` (or a second crop) in the About
`.shot`, or use a simple navy panel — builder's choice, must render cleanly.

- [ ] **Step 2: Render and verify**

Refresh `http://localhost:8765`. Confirm: 3 service cards in a row (stack on narrow width), Case
Reviews copy present, About shows bio + all 6 credential items, gold eyebrow labels on each section.

- [ ] **Step 3: Commit**

```bash
git add jmc/index.html
git commit -m "jmc: add services, case reviews, and about sections"
```

---

### Task 4: Add Contact section with Google Calendar booking + footer

**Files:**
- Modify: `jmc/index.html`

- [ ] **Step 1: Add contact section with live booking embed + button + email, then footer**

Append the navy `#contact` section. Include:
- An `<iframe>` embedding the booking page (best-effort; Google may block framing, so the button is the real path):
  `<iframe src="https://calendar.app.google/hVWEfvB3Ns5hUcqf7" style="width:100%;height:320px;border:0;border-radius:10px;background:#fff" title="Book a consultation"></iframe>`
- A primary button link (white-on-navy): `<a class="btn-primary" href="https://calendar.app.google/hVWEfvB3Ns5hUcqf7" target="_blank" rel="noopener">Open my booking calendar →</a>`
- Business email: `<a href="mailto:michaelcoleman@thecharteddefense.org">michaelcoleman@thecharteddefense.org</a>`
Then the `<footer>` with "© JMC Consulting, LLC · Madison, Alabama" and the informational/not-legal-advice disclaimer (spec §5).

- [ ] **Step 2: Render and verify the booking link works**

Refresh `http://localhost:8765`. Confirm: contact section is navy, booking button present. Click the
button → it should open `https://calendar.app.google/hVWEfvB3Ns5hUcqf7` in a new tab and show Michael's
real Google appointment page. (If the iframe shows blank/refused, that's expected — the button is the
guaranteed path; leave the iframe as a progressive enhancement.)

- [ ] **Step 3: Commit**

```bash
git add jmc/index.html
git commit -m "jmc: add contact/booking section and footer"
```

---

### Task 5: Mobile responsiveness pass + final local verification

**Files:**
- Modify: `jmc/index.html` (add/confirm `@media` rules)

- [ ] **Step 1: Confirm/添加 mobile media queries**

Ensure a `@media (max-width: 760px)` block: hero grid → 1 column (text above image), `.cards` →
1 column, `.about .wrap` and `.cgrid` → 1 column, hero `h1` font-size reduced (~36px), nav `.links`
hidden or wrapped so the bar doesn't overflow. Add if missing.

- [ ] **Step 2: Verify at mobile width**

In the browser at `http://localhost:8765`, narrow the window to ~390px (or device toolbar). Confirm:
no horizontal scroll, hero stacks, cards stack, headshot scales, CTA button is full-width and tappable.

- [ ] **Step 3: Verify all anchor links jump correctly**

Click each nav link (Services, Case Reviews, About Mike Coleman, Contact) → page scrolls to the right
section.

- [ ] **Step 4: Stop the preview server and commit**

```bash
kill %1 2>/dev/null
git add jmc/index.html
git commit -m "jmc: responsive layout and final polish"
```

---

### Task 6: Add `jmc/netlify.toml` (build config for site B)

**Files:**
- Create: `jmc/netlify.toml`

- [ ] **Step 1: Write the config (mirrors .com security headers, publishes the jmc folder)**

```toml
[build]
  publish = "."

[[headers]]
  for = "/*"
  [headers.values]
    X-Frame-Options = "SAMEORIGIN"
    X-Content-Type-Options = "nosniff"
    Referrer-Policy = "strict-origin-when-cross-origin"
    Permissions-Policy = "camera=(), microphone=(), geolocation=()"

[[headers]]
  for = "/index.html"
  [headers.values]
    Cache-Control = "public, max-age=300"
```
NOTE: `X-Frame-Options` is `SAMEORIGIN` (not `DENY`) so the page itself isn't framed by others;
the Google Calendar iframe is unaffected because that header governs *this* site being framed.

- [ ] **Step 2: Commit and push (this makes the code available to Netlify; nothing is live until the Netlify site is created in Task 7)**

```bash
git add jmc/netlify.toml
git commit -m "jmc: add Netlify build config for .org site"
git push origin main
```
Expected: push succeeds. The existing `.com` Netlify site will rebuild from root and is unaffected
(it ignores the new `jmc/` subfolder).

---

### Task 7: Deploy wiring — Netlify site B + Cloudflare DNS (guided, manual)

This task is performed by Michael in the browser with step-by-step guidance. No code.

- [ ] **Step 1: Create the second Netlify site**
  1. Log in at https://app.netlify.com → **Add new site → Import an existing project**.
  2. Choose GitHub → repo **mikecuab/thecharteddefense-website**.
  3. **Base directory:** `jmc`  ·  **Publish directory:** `jmc` (or `.` relative to base)  ·  Build command: empty.
  4. Deploy. Confirm the temporary `*.netlify.app` URL shows the JMC page.

- [ ] **Step 2: Assign the custom domain in Netlify**
  1. Site B → **Domain management → Add a domain** → `thecharteddefense.org` (and `www.thecharteddefense.org`).
  2. Netlify shows the DNS target (a CNAME/A or `*.netlify.app` alias). Note it.

- [ ] **Step 3: Remove the old redirect and point DNS in Cloudflare**
  1. Log in at https://dash.cloudflare.com → select **thecharteddefense.org**.
  2. **Rules / Redirect Rules / Bulk Redirects / Page Rules:** find and **delete** the rule that
     forwards `.org → .com`.
  3. **DNS → Records:** point the apex (`@`) and `www` to the Netlify target from Step 2
     (per Netlify's instructions — usually a CNAME to the site's `*.netlify.app`, proxied OFF/grey-cloud
     initially so Netlify can issue SSL).
  4. Wait for DNS propagation; let Netlify provision the Let's Encrypt certificate.

- [ ] **Step 4: Verify end-to-end**
  1. Visit `https://thecharteddefense.org` → JMC Consulting page loads (no redirect to `.com`).
  2. Visit `https://thecharteddefense.com` → unchanged Charted Defense site.
  3. Click the booking CTA on `.org` → Google appointment page opens.
  4. Load `.org` on a phone → responsive layout confirmed.

---

## Self-Review Notes

- **Spec coverage:** §4 hosting → Tasks 6–7; §5 structure → Tasks 2–4; §6 brand/assets → Tasks 1–2;
  §7 booking link → Task 4 (live link, not placeholder); §8 files → Tasks 1–6; §9 success criteria → Task 7 Step 4. All covered.
- **No placeholders:** booking URL is the real `calendar.app.google/hVWEfvB3Ns5hUcqf7` throughout.
- **Asset paths consistent:** `images/jmc-logo.png` and `images/michael-coleman-consulting.png` used in Tasks 1, 2, 3.
- **`.com` safety:** root `netlify.toml` unchanged; site B isolated by base directory `jmc`.
