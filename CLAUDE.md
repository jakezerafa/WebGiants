# CLAUDE.md — Frontend Website Rules

## Always Do First
- **Invoke the `frontend-design` skill** before writing any frontend code, every session, no exceptions.

## Reference Images
- If a reference image is provided: match layout, spacing, typography, and color exactly. Swap in placeholder content (images via `https://placehold.co/`, generic copy). Do not improve or add to the design.
- If no reference image: design from scratch with high craft (see guardrails below).
- Screenshot your output, compare against reference, fix mismatches, re-screenshot. Do at least 2 comparison rounds. Stop only when no visible differences remain or user says so.

## Local Server
- **Always serve on localhost** — never screenshot a `file:///` URL.
- Start the dev server: `node serve.mjs` (serves the project root at `http://localhost:3000`)
- `serve.mjs` lives in the project root. Start it in the background before taking any screenshots.
- If the server is already running, do not start a second instance.

## Screenshot Workflow
- Puppeteer is installed at `C:/Users/nateh/AppData/Local/Temp/puppeteer-test/`. Chrome cache is at `C:/Users/nateh/.cache/puppeteer/`.
- **Always screenshot from localhost:** `node screenshot.mjs http://localhost:3000`
- Screenshots are saved automatically to `./temporary screenshots/screenshot-N.png` (auto-incremented, never overwritten).
- Optional label suffix: `node screenshot.mjs http://localhost:3000 label` → saves as `screenshot-N-label.png`
- `screenshot.mjs` lives in the project root. Use it as-is.
- After screenshotting, read the PNG from `temporary screenshots/` with the Read tool — Claude can see and analyze the image directly.
- When comparing, be specific: "heading is 32px but reference shows ~24px", "card gap is 16px but should be 24px"
- Check: spacing/padding, font size/weight/line-height, colors (exact hex), alignment, border-radius, shadows, image sizing

## Output Defaults
- Single `index.html` file, all styles inline, unless user says otherwise
- Tailwind CSS via CDN: `<script src="https://cdn.tailwindcss.com"></script>`
- Placeholder images: `https://placehold.co/WIDTHxHEIGHT`
- Mobile-first responsive

## Brand Assets
- Always check the `brand_assets/` folder before designing. It may contain logos, color guides, style guides, or images.
- If assets exist there, use them. Do not use placeholders where real assets are available.
- If a logo is present, use it. If a color palette is defined, use those exact values — do not invent brand colors.

## Anti-Generic Guardrails
- **Colors:** Never use default Tailwind palette (indigo-500, blue-600, etc.). Pick a custom brand color and derive from it.
- **Shadows:** Never use flat `shadow-md`. Use layered, color-tinted shadows with low opacity.
- **Typography:** Never use the same font for headings and body. Pair a display/serif with a clean sans. Apply tight tracking (`-0.03em`) on large headings, generous line-height (`1.7`) on body.
- **Gradients:** Layer multiple radial gradients. Add grain/texture via SVG noise filter for depth.
- **Animations:** Only animate `transform` and `opacity`. Never `transition-all`. Use spring-style easing.
- **Interactive states:** Every clickable element needs hover, focus-visible, and active states. No exceptions.
- **Images:** Add a gradient overlay (`bg-gradient-to-t from-black/60`) and a color treatment layer with `mix-blend-multiply`.
- **Spacing:** Use intentional, consistent spacing tokens — not random Tailwind steps.
- **Depth:** Surfaces should have a layering system (base → elevated → floating), not all sit at the same z-plane.

## Reusable Components

### Google Reviews Integration
When the user asks to "insert the Google reviews integration", add this section to the page. Place it after the testimonials section (or wherever the user specifies).

**How it works:**
- User taps 1–3 stars → internal feedback form appears ("How can we improve our service?" + textarea + submit)
- User taps 4–5 stars → a card appears with a "Leave a Google review" button (opens in new tab — no auto-redirect)
- After submitting low-rating feedback → thank you confirmation shown

**Insert this HTML block:**
```html
<!-- GOOGLE REVIEWS SECTION -->
<section style="padding:100px 24px;border-top:1px solid rgba(255,255,255,0.06);position:relative;overflow:hidden;">
  <div style="position:absolute;top:0;left:50%;transform:translateX(-50%);width:700px;height:400px;background:radial-gradient(ellipse at 50% 0%,rgba(30,111,255,0.08) 0%,transparent 70%);pointer-events:none;"></div>
  <div style="max-width:560px;margin:0 auto;text-align:center;position:relative;">
    <h2 class="t-title" style="margin-bottom:12px;">How did we do?</h2>
    <p style="font-size:15px;color:var(--text-secondary);margin-bottom:40px;">Your feedback helps us grow. Tap a star to rate your experience.</p>
    <div id="gr-stars" style="display:flex;justify-content:center;gap:12px;margin-bottom:40px;">
      <button onclick="grRate(1)" onmouseenter="grHover(1)" onmouseleave="grHoverOut()" class="gr-star" aria-label="1 star">★</button>
      <button onclick="grRate(2)" onmouseenter="grHover(2)" onmouseleave="grHoverOut()" class="gr-star" aria-label="2 stars">★</button>
      <button onclick="grRate(3)" onmouseenter="grHover(3)" onmouseleave="grHoverOut()" class="gr-star" aria-label="3 stars">★</button>
      <button onclick="grRate(4)" onmouseenter="grHover(4)" onmouseleave="grHoverOut()" class="gr-star" aria-label="4 stars">★</button>
      <button onclick="grRate(5)" onmouseenter="grHover(5)" onmouseleave="grHoverOut()" class="gr-star" aria-label="5 stars">★</button>
    </div>
    <div id="gr-google" style="display:none;animation:fadeInUp 0.4s cubic-bezier(.16,1,.3,1) both;">
      <div style="background:var(--elevated);border:1px solid rgba(66,133,244,0.3);border-radius:16px;padding:32px;text-align:center;">
        <p style="font-size:16px;font-weight:600;color:#fff;margin-bottom:6px;">We're glad you enjoyed it!</p>
        <p style="font-size:14px;color:var(--text-muted);margin-bottom:24px;">Would you mind sharing your experience on Google?</p>
        <a href="GOOGLE_REVIEW_URL_HERE" target="_blank" rel="noopener" class="btn-primary" style="display:inline-flex;align-items:center;gap:10px;justify-content:center;text-decoration:none;">
          <svg width="18" height="18" viewBox="0 0 48 48" xmlns="http://www.w3.org/2000/svg" style="flex-shrink:0;">
            <path fill="#EA4335" d="M24 9.5c3.54 0 6.71 1.22 9.21 3.6l6.85-6.85C35.9 2.38 30.47 0 24 0 14.62 0 6.51 5.38 2.56 13.22l7.98 6.19C12.43 13.72 17.74 9.5 24 9.5z"/>
            <path fill="#4285F4" d="M46.98 24.55c0-1.57-.15-3.09-.38-4.55H24v9.02h12.94c-.58 2.96-2.26 5.48-4.78 7.18l7.73 6c4.51-4.18 7.09-10.36 7.09-17.65z"/>
            <path fill="#FBBC05" d="M10.53 28.59c-.48-1.45-.76-2.99-.76-4.59s.27-3.14.76-4.59l-7.98-6.19C.92 16.46 0 20.12 0 24c0 3.88.92 7.54 2.56 10.78l7.97-6.19z"/>
            <path fill="#34A853" d="M24 48c6.48 0 11.93-2.13 15.89-5.81l-7.73-6c-2.18 1.48-4.97 2.31-8.16 2.31-6.26 0-11.57-4.22-13.47-9.91l-7.98 6.19C6.51 42.62 14.62 48 24 48z"/>
          </svg>
          Leave a Google review
        </a>
      </div>
    </div>
    <div id="gr-feedback" style="display:none;animation:fadeInUp 0.4s cubic-bezier(.16,1,.3,1) both;">
      <div style="background:var(--elevated);border:1px solid var(--border);border-radius:16px;padding:32px;text-align:left;">
        <p style="font-size:15px;font-weight:600;color:#fff;margin-bottom:6px;">How can we improve our service?</p>
        <p style="font-size:13px;color:var(--text-muted);margin-bottom:20px;">We read every response and take action.</p>
        <textarea id="gr-text" rows="4" placeholder="Tell us what we could do better…" style="width:100%;background:rgba(255,255,255,0.04);border:1px solid rgba(255,255,255,0.1);border-radius:10px;padding:14px;font-size:14px;color:#fff;font-family:inherit;resize:vertical;outline:none;transition:border-color 0.2s ease;" onfocus="this.style.borderColor='rgba(30,111,255,0.5)'" onblur="this.style.borderColor='rgba(255,255,255,0.1)'"></textarea>
        <button onclick="grSubmitFeedback()" class="btn-primary" style="margin-top:16px;width:100%;justify-content:center;">Send feedback</button>
      </div>
    </div>
    <div id="gr-thanks" style="display:none;animation:fadeInUp 0.4s cubic-bezier(.16,1,.3,1) both;">
      <div style="background:var(--elevated);border:1px solid rgba(52,168,83,0.3);border-radius:16px;padding:32px;">
        <div style="font-size:32px;margin-bottom:12px;">✓</div>
        <p style="font-size:16px;font-weight:600;color:#34A853;margin-bottom:6px;">Thank you for your feedback!</p>
        <p style="font-size:14px;color:var(--text-muted);">We'll use it to keep improving our service.</p>
      </div>
    </div>
  </div>
</section>

<style>
  .gr-star { font-size:40px;color:rgba(255,255,255,0.15);background:none;border:none;cursor:pointer;padding:4px;line-height:1;transition:color 0.15s ease,transform 0.15s cubic-bezier(.34,1.56,.64,1); }
  .gr-star.active { color:#FBBC04; }
  .gr-star:focus-visible { outline:2px solid #FBBC04;border-radius:4px; }
</style>

<script>
  let grSelected = 0;
  const grStars = () => document.querySelectorAll('.gr-star');
  function grHighlight(n) { grStars().forEach((s,i) => s.classList.toggle('active', i < n)); }
  function grHover(n) { grHighlight(n); }
  function grHoverOut() { grHighlight(grSelected); }
  function grRate(n) {
    grSelected = n;
    grHighlight(n);
    document.getElementById('gr-feedback').style.display = 'none';
    document.getElementById('gr-thanks').style.display = 'none';
    document.getElementById('gr-google').style.display = 'none';
    if (n >= 4) {
      setTimeout(() => { document.getElementById('gr-google').style.display = 'block'; }, 200);
    } else {
      setTimeout(() => { document.getElementById('gr-feedback').style.display = 'block'; }, 200);
    }
  }
  function grSubmitFeedback() {
    const text = document.getElementById('gr-text').value.trim();
    if (!text) return;
    document.getElementById('gr-feedback').style.display = 'none';
    document.getElementById('gr-thanks').style.display = 'block';
    // TODO: send `text` and `grSelected` to backend / form handler
  }
</script>
```

**Before inserting:** ask the user for their Google review link (format: `https://search.google.com/local/writereview?placeid=PLACE_ID`) and replace `GOOGLE_REVIEW_URL_HERE`. If they don't have it yet, insert the placeholder and leave a comment.

**Styling notes:** The component uses CSS variables (`--elevated`, `--border`, `--text-secondary`, `--text-muted`, `btn-primary`) from the WebGiants design system. For other sites, map these to the site's equivalent variables or replace with hardcoded values. Star color is Google yellow `#FBBC04`. Success state uses Google green `#34A853`.

---

## Hard Rules
- Do not add sections, features, or content not in the reference
- Do not "improve" a reference design — match it
- Do not stop after one screenshot pass
- Do not use `transition-all`
- Do not use default Tailwind blue/indigo as primary color
