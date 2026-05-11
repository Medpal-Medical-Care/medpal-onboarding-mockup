# Onboarding Mockup - Production-Polish Rebuild

**Date:** 2026-05-11
**Repo:** `Medpal-Medical-Care/medpal-onboarding-mockup`
**File touched:** `index.html` (clean-slate rewrite)
**Deploy:** local only - do not commit/push until user explicitly says "push"

## Goal

Take the current 14-screen onboarding mockup (login + 12 steps + done) and rebuild as a single self-contained `index.html` that reads as a polished modern-SaaS product (Linear/Stripe/Vercel direction). Output must (a) raise the visual bar on the mockup, and (b) produce a tokenized design system that ports cleanly into the live `medpal-onboarding-web` Next.js app afterwards.

## Decisions locked in brainstorming

| Decision | Value |
|---|---|
| Aesthetic target | Modern SaaS (Linear / Stripe / Vercel) |
| Approach | Clean-slate rewrite of `index.html` |
| Brand orange shell | Keep full-bleed gradient background (open to softening) |
| Icon system | Lucide (inline SVG in mockup, `lucide-react` in production port) |
| Add signup screen | Yes - between login and step 1 |
| Commit/push | Do not push; local only until user says "push" |

Screens (final list): **login → signup (new) → step1…step12 → done** = 15 screens.

---

## Section 1 - Design Tokens

All values live in `:root` CSS variables. No inline styles in screens.

### Color

**Brand** (kept):
```
--brand-50:  #FFF4ED;   --brand-100: #FFE4D1;
--brand-200: #FFCDA8;   --brand-300: #FDB07C;
--brand-400: #F58851;   --brand-500: #EE8A45;   /* gradient start */
--brand-600: #EB5A3D;   /* gradient end */
--brand-700: #C9421F;   --brand-800: #9F2F12;
--brand-gradient: linear-gradient(135deg, #EE8A45 0%, #EB5A3D 100%);
```

**Neutral** (slate - cooler / more tech than current warm grays):
```
--gray-0…--gray-900  /* Tailwind slate */
```

**Semantic** - each with `-50` `-500` `-700`:
- `--success-*` (emerald)
- `--warning-*` (amber)
- `--danger-*` (red)
- `--info-*` (blue)

### Typography

- `--font-display: "Plus Jakarta Sans"` - display, brand chrome, buttons, step pills
- `--font-body: "Inter"` - everything else, with `font-feature-settings: "cv11","ss01","ss03"`
- Scale (tighter than current 15px base): `--text-xs 11` `--text-sm 12` `--text-base 13` `--text-md 14` `--text-lg 16` `--text-xl 18` `--text-2xl 22` `--text-3xl 28`
- Line heights: `--lh-tight 1.2` / `--lh-snug 1.35` / `--lh-normal 1.5` / `--lh-loose 1.65`
- Weights: `--fw-medium 500` / `--fw-semi 600` / `--fw-bold 700`
- Tracking: `--tracking-tight -0.02em` (display) / `--tracking-normal 0` / `--tracking-wide 0.04em` (uppercase eyebrows)

### Spacing

4px scale: `--space-1 4` `--space-2 8` `--space-3 12` `--space-4 16` `--space-5 20` `--space-6 24` `--space-8 32` `--space-10 40` `--space-12 48` `--space-16 64`.

### Radius

`--r-sm 6` `--r-md 8` `--r-lg 12` `--r-xl 16` `--r-pill 999`.

### Shadow (lower elevation than current)

```
--shadow-xs: 0 1px 2px rgba(15,23,42,0.04);
--shadow-sm: 0 1px 3px rgba(15,23,42,0.06), 0 1px 2px rgba(15,23,42,0.04);
--shadow-md: 0 4px 12px rgba(15,23,42,0.08);
--shadow-lg: 0 12px 32px -8px rgba(15,23,42,0.18);
--ring-brand: 0 0 0 3px rgba(238,138,69,0.25);   /* focus ring */
--ring-danger: 0 0 0 3px rgba(239,68,68,0.20);
```

### Motion

```
--ease-out: cubic-bezier(0.22, 1, 0.36, 1);
--ease-spring: cubic-bezier(0.34, 1.56, 0.64, 1);   /* switch thumb only */
--dur-fast: 120ms;  --dur-base: 180ms;  --dur-slow: 280ms;
```

`prefers-reduced-motion: reduce` zeros all transitions.

---

## Section 2 - Components

All components built from tokens. No inline overrides allowed in screens.

### Button
- Base: `radius md` (8px, not pill), weight semi, padding 10/20, transition fast
- Variants: `--primary` (gradient + shadow-sm), `--secondary` (white + gray-300 border), `--ghost` (transparent + gray-600), `--danger` (danger-500), `--sm`, `--lg`
- `:focus-visible` → `--ring-brand`
- `:active` → `scale(0.98)`
- `:disabled` → opacity 0.5

### Input / Select / Textarea
- 1.5px border `gray-300`, radius md, padding 10/12, text-md, bg white
- `:hover` border `gray-400`
- `:focus` border `brand-500` + `--ring-brand` (no `outline`)
- `:disabled` bg `gray-50`
- `.input--error` border `danger-500` + `--ring-danger`

### Field
- Structure: `<label>` + control + `.hint` (text-sm gray-500) + `.field-error` (text-sm danger-600 + AlertCircle icon)
- Required indicator: small `·` glyph instead of red `*` (calmer)
- `aria-required="true"` for screen readers

### Chip
- Radius pill, padding 6/14, text-sm weight medium, border 1px gray-300, bg white
- `:hover` bg gray-50
- `.chip--selected` brand-gradient bg + white text + no border
- `.chip--suggestion` dashed brand-300 border + brand-700 text + brand-50 hover

### Card (screen shell)
- bg white, radius lg
- `.card-header` shows ONLY an eyebrow "STEP N OF 12" (text-xs uppercase tracking-wide gray-500). Drops the gradient pill that currently duplicates the h1.
- `.card-body` padding-x `space-8`, padding-y `space-6`
- `.card-footer` bg gray-50, border-top gray-200, padding 16/32, flex space-between

### Banner (above forms - only when there's a real secondary message)
- Variants: `--info` `--success` `--warning` `--danger` - each with semantic bg + text + Lucide icon
- Drops the universal-orange info-banner pattern

### Switch
- 38×22, radius pill, transition base ease-spring
- `--on` → bg brand-500, thumb slides right + slight scale pop

### Upload card
- Dashed 1.5px gray-300, radius md, bg gray-50
- Lucide icon (Upload / FileText / IdCard / ShieldCheck) - no emoji
- `--captured` solid brand-500 + check badge
- `--error` solid danger-500 + XCircle

### Step pill (folder tab - concept retained)
- Base: flex 1, padding 8/4 10, top radius sm, bottom flat
- `bg rgba(white,0.18)` over gradient, color rgba(white,0.85)
- `--done` bg rgba(white,0.40), color brand-800
- `--active` bg white, color gray-900, padding 14/6, shadow top+sides only (melts into card)
- transition padding/bg via `dur-slow ease-out`

---

## Section 3 - Screens

### Shell pattern (all onboarding screens 1–12)

```
[ orange gradient bg ]
  app-shell (max-w 1024)
    app-header
      logo + "MedPal Provider" wordmark   |   Help (HelpCircle icon) · Logout (ghost)
    step-pills (folder tabs)
    card
      card-header: "STEP N OF 12" eyebrow only
      card-body:
        h1 (display, tracking-tight)            ← single source of "what page is this"
        subtitle (gray-600 text-md)             ← replaces most info-banners
        [banner --info]  (only if real secondary message)
        [form content]
      card-footer: ← Back   |   Continue →
```

**Header chrome simplifications:**
- Drop "Join MedPal" sub-tag
- Help → Lucide HelpCircle (icon-only with tooltip)
- Logout → ghost-style button
- Submit button: "Submit application" (no checkmark glyph)

### Screen-by-screen plan

| # | ID | Title | Subtitle | Notes |
|---|---|---|---|---|
| 0a | `login` | Welcome back | Sign in to manage your provider account | Centered card on neutral bg (no gradient shell on auth screens) |
| 0b | **`signup`** (new) | Create your provider account | Start your application to join MedPal | First+Last, Email, Password, Confirm; TOS micro-copy; optional OAuth row; bottom link → Login |
| 1 | `step1` | Personal information | Your name and contact details | 2-col grid, tighter gaps |
| 2 | `step2` | Credentials | Your degree, license, and NPI | 2-col; **remove Roles chips from this step** (they're already on Step 4 - deduplicate) |
| 3 | `step3` | Mailing address | Where official documents are sent | Address autocomplete; collapse Country into a small "Use non-US address" link |
| 4 | `step4` | About you | A short bio patients see on your profile | 2-col: avatar+role(chips) left / bio right; Polish gets Lucide Sparkles + primary color (not random teal); inline char counter |
| 5 | `step5` | State licenses | Each state where you can practice | Per-state card; **collapsed summary view by default** once filled; + Add state |
| 6 | `step6` | Diplomas & certifications | Documents that appear on your profile | 2-col strip uploads; Lucide GraduationCap / Award |
| 7 | `step7` | Certificate of insurance | Current malpractice liability coverage | 2-col fields + upload; Lucide ShieldCheck |
| 8 | `step8` | Identity verification - video | A short selfie video holding your ID | Renamed from "Verification Video"; 2-col video left / tips+status right |
| 9 | `step9` | Pricing | Set your session rates | **Restructured**: Section A "Telehealth" (video+voice 2x2) → divider → Section B "In-person" with switch *as section header* (toggling collapses Section B body) |
| 10 | `stepid` | Identity documents | Driver's license or state ID | 2-col upload-grid + auto-fill banner; Lucide IdCard |
| 11 | `step10` | Emergency contact | Required only if you offer in-person visits | 2-col; auto-hidden when in-person off in step 9 |
| 12 | `step11` | Specialty & tags | Help patients find you | **Restructured** - see below |
| 13 | `done` | You're all set | We'll review your application within 1–2 business days | Lucide CheckCircle; what-happens-next ordered list stays |

### Step 12 - Specialty

Flat chip list matching live `select-specialty-form.tsx` (which fetches a flat alphabetical list from Firestore `specialty_list`). No client-side grouping - keep parity with live structure.

The grouped layout previously considered was rolled back on 2026-05-11; the spec section below is preserved for historical context only.

~~Previously proposed layout:~~
  - Primary care: Internal Medicine · Pediatrics · Family Medicine · Geriatrics
  - Surgery & musculoskeletal: Orthopedics · Surgery · Neurosurgeon · Sports Medicine · Physical Therapy · Chiropractic
  - Mental health & cognition: Psychiatry · Psychologist · Neurology
  - Specialty medicine: Cardiology · Dermatology · Gastroenterology · Oncology · Pulmonology · Urology · Gynecology · Otolaryngology · Wound care · Pain management · Radiologist
  - Acute & critical: Emergency Medicine · Critical care
  - Allied + other: Dentist · Nurse
- **Add custom specialty** row with Validate button; rejection state shows "Did you mean:" + dashed suggestion chips
- **Specialty tags (optional)** with search box + selected-tag chips (× to remove)

Groups are mockup-time grouping only - data model stays a flat list. Production port can derive groupings client-side from the same flat list.

---

## Section 4 - Motion, States, Responsive, Accessibility

### Motion
- All transitions use `--ease-out`; three durations only (fast/base/slow)
- `--ease-spring` reserved for switch thumb pop
- Screen transitions: opacity 0→1 fade-in 180ms on `.screen.active` (no horizontal jump - `scrollbar-gutter: stable` retained)
- `prefers-reduced-motion: reduce` → all transitions zeroed

### State matrix (every interactive component covers all 6)

| Component | default | hover | focus-visible | active/selected | disabled | error |
|---|---|---|---|---|---|---|
| Button | ✓ | bg shift | ring-brand | scale 0.98 | opacity 0.5 | - |
| Input | ✓ | border gray-400 | ring-brand + border brand-500 | - | bg gray-50 | border danger-500 + ring-danger |
| Chip | ✓ | bg gray-50 | ring-brand | brand-gradient bg | - | - |
| Switch | ✓ | - | ring-brand | brand-500 thumb-right spring | - | - |
| Upload | ✓ | brand-400 border | ring-brand | captured (brand-500 + Check) | - | danger-500 + XCircle |
| Step pill | ✓ | bg lift +0.10 alpha | ring on white outline | white card + lift | - | - |

### Empty / loading / error
- Polish bio: button → Lucide Loader2 spin + "Polishing…" label, disabled
- Upload: dashed border pulses + "Uploading…" label
- Specialty search: "No specialties match 'xyz'" + link to "Add as custom"
- Form submit: inline `.field-error` per offending field + banner-danger at top of card-body summarizing count

### Responsive

| Breakpoint | Card max-width | Step pills | Form grids |
|---|---|---|---|
| ≥1024 | 1024px | 12 in one row | 2-col where defined |
| 768–1023 | 100% − 32px | wrap to 2 rows (6 + 6) | stay 2-col |
| <768 | 100% − 16px | horizontal scroll strip + step-N-of-12 dots | single col |

Step 4 (avatar+bio) and Step 8 (video+tips) collapse to single col under 768.

### Accessibility
- Icons: `aria-hidden="true"` + sr-only text where icon is the only label (Help)
- WCAG AA contrast: gray-500 on gray-50 = 4.6:1; brand-700 on brand-50 = 5.1:1
- `:focus-visible` ring visible on every interactive element
- Real `<label for>`, no placeholder-as-label
- Required = `*` visual + `aria-required="true"`

---

## Section 5 - File output

Single self-contained `index.html`. `<style>` block ordered:
1. Token CSS variables (`:root`)
2. Reset + base typography
3. Layout primitives (`.app-shell`, `.card`, grids)
4. Components (`.btn`, `.input`, `.chip`, `.banner`, `.switch`, `.upload`, `.step-pill`)
5. Screen-specific overrides (minimal - most screens use components as-is; `.screen-stepN` namespace when needed)
6. Animations + responsive media queries

JS: vanilla, slightly more than current - search-filter on Step 12, switch collapse on Step 9, signup ↔ login toggle. No build step.

Asset additions: Lucide SVG sprite (subset - only icons actually used: ArrowRight, ArrowLeft, HelpCircle, Sparkles, Upload, FileText, GraduationCap, Award, ShieldCheck, IdCard, CheckCircle, AlertCircle, XCircle, Loader2, X). Inlined as `<symbol>` for `<use>` reuse.

---

## Parity vs. production (2026-05-11 update)

The mockup is a UI/UX deliverable only; it does **not** introduce new stored datapoints relative to `medpal-onboarding-web`. Concrete parity decisions:

- **Step 1 Personal:** First, Middle (opt), Last, Gender (radio M/F), Phone country, Phone. Live does NOT collect Title, DOB, Email, or Pronouns.
- **Step 2 Credentials:** Provider title (select), Highest degree (select), Other degree (opt), Practice name, Mental Health Specialist toggle. NPI moves to Step 5.
- **Step 3 Address:** line1, line2 (opt), City, State, ZIP, Country (disabled "United States"). The "Use as business address" toggle is dropped - live doesn't have it.
- **Step 5 Practice details:** NPI at top, then per-state cards: State + Expiration + License document + Work addresses (up to 3 per state). License number + Issue date dropped.
- **Step 7 Certificate of Insurance:** **Attestation only.** "I certify that I hold a valid Certificate of Insurance, as required by my state of licensure." Accepted/Declined registered; no document upload, no carrier/policy/dates fields.
- **Step 9 Pricing:** Telehealth video + voice rates (provider + advocate), in-person toggle, in-person rates, travel distance (slider). Removed earlier conditional "Per N-minute session" display because production does not flip duration display based on MH toggle (the MH flag governs server-side session length only).
- **Step 11 Emergency contact:** Conditional on Step 9's in-person toggle. When in-person is OFF, the step pill is hidden and the Continue/Back buttons skip past it.

**The only NEW addition vs production:** Step 10 (Identity Documents - driver's license / state ID front + back). Tagged with a small `NEW` badge in the step pill. When porting this mockup to `medpal-onboarding-web`, this step will need to be implemented from scratch (component + storage + UI). Every other step is a re-skin of an existing component.

## Out of scope

- Real form validation logic (mockup stays mockup)
- Actual submit / route / state management
- Patient-side flows
- Provider iOS / Android counterparts
- Pushing to remote / GitHub Pages rebuild (local only until user says "push")

## Done means

- `index.html` rewritten clean-slate
- Every screen visible via mockup nav (15 screens including signup)
- Zero inline `style="…"` attributes
- All emoji replaced with Lucide SVG
- User previewed locally and signed off on the result

---

## Port-back plan (out of scope for this rewrite, but informs design)

When the mockup is approved, the token block + component CSS lift directly into `medpal-onboarding-web` (Next.js global CSS or Tailwind `@layer base` + `@layer components`). Lucide icons become `lucide-react` imports (already a dep). Most screen JSX changes are: drop duplicate page-title pill, add subtitle line, swap inline styles for utility classes, swap emoji icons for `lucide-react` components. Per-screen complexity is small once the tokens land.
