# Famous Frank's — demo site

Single-page static site for Famous Frank's, "The All-American Original" — the
late-night truck at 30 Pierce St, West Lafayette, and the Franks station at
Purdue's Cary Knight Spot. No build step, no dependencies: `index.html` carries
its own CSS and JS inline. Fonts come from Google Fonts.

## Live

| | |
| --- | --- |
| **Production** | https://famous-franks.vercel.app |
| **Repo** | https://github.com/ydayal5/Franks |
| **Vercel project** | `yash-dayal-s-projects/famous-franks` |

The GitHub repo is connected to the Vercel project, so **every push to `main`
deploys automatically**. No CI config needed — it's a static deploy.

### Private preview (artifact)

https://claude.ai/code/artifact/0a208891-55b1-429b-b2c3-c41ed11d1b78

Private until shared from its share menu — useful for sending the client a look
before the Vercel URL is public-facing. Regenerate it after editing
`index.html` by stripping the document wrapper (the Artifact host supplies its
own charset, viewport and reset):

```
node -e "let f=require('fs'),s=f.readFileSync('index.html','utf8');s=s.slice(s.indexOf('<title>')).replace('</head>','').replace('<body>','').replace(/<\/body>\s*<\/html>\s*$/,'');f.writeFileSync('franks-artifact.html',s)"
```

## Repo note

`ui-ux-pro-max-skill-main/` (23 MB) and the original `Franks Logo.jpeg` sit in
this folder. Both are listed in `.vercelignore` so they are not uploaded on
deploy — the web-ready copy the page actually loads is `franks-logo.jpg`.

## Deploy

Pushing to `main` is the deploy. To ship from the CLI instead:

```
npx vercel           # preview deployment
npx vercel --prod    # production
```

`vercel.json` sets clean URLs and four security headers (verified live:
`X-Content-Type-Options`, `Referrer-Policy`, `X-Frame-Options`,
`Permissions-Policy`). Static deploy, no build step. Local preview:
`npx serve .`

`vercel link` writes `.env.local` containing a `VERCEL_OIDC_TOKEN` — it is
gitignored and must stay that way.

## Structure

| Section | Notes |
| --- | --- |
| Hero | "Go Fat or Go Home", mascot badge, live open/closed status |
| Three ways | The Truck / The Knight Spot / DoorDash |
| The grub | The real board, styled like the one bolted to the truck |
| Merch | Hoodies, crew necks, tees — real prices off the board |
| Knight Spot | Full section, sky-blue field, meal swipes |
| Catering | Famous Franks 2, Greek life lead |
| Story | 1996 to now, plus the 100% health inspection |
| Find us | Address, Harry's landmark, map links, socials, hero photo slot |
| Get in touch | One form, catering fields reveal conditionally |

Plus `Restaurant` JSON-LD at the bottom of `index.html` — address, hours,
payment methods, `sameAs` Facebook, and the "All-American Original" slogan.

## The open/closed engine

All config sits in the block at the top of the `<script>` near the end of
`index.html`.

- `SERVICE_DAYS` / `OPEN_HOUR` / `CLOSE_HOUR` — Thu–Sat, 23:00 to 03:30.
- `SESSIONS` and `BREAKS` — **the Purdue academic calendar.** The truck only
  runs while campus is in session, so the engine says *"Closed for spring
  break"* rather than just "Closed".
- Read in `America/Indiana/Indianapolis`, so out-of-state visitors still see
  Purdue's clock. 1am Saturday correctly counts as Friday's service night.

States: `Open now` to `Last call` (final 45 min) to `Opens in 4h 12m` to
`Next: Thu 11PM` to `Closed for Thanksgiving break`. The hero headline changes
with each.

**Update `SESSIONS` and `BREAKS` every year** against purdue.edu/registrar. The
dates in there now are approximate for 2026–27 and need checking.

## The form

`FORM_ENDPOINT` is empty, so the form validates, copies the composed message to
the clipboard, and points people at Facebook — honest about being a demo, and
it still solves the real problem that there is no phone number.

Set `FORM_ENDPOINT` to a Formspree URL or a Vercel function and it POSTs JSON
instead, with the clipboard path as the fallback if the request fails.

## Where the content came from

Menu items, descriptions and prices are **transcribed from photographs of the
truck's own "Get Your Grub On" board** — not invented, not reconstructed from
reviews. Same for the brand lines: *The All-American Original*, *Go Fat or Go
Home*, *Slinging great munchies since 1996*, and the credit-card surcharge
notice are all lifted from the truck and their merch.

Items whose names are legible on the board but whose descriptions and prices
are not appear under **"Also on the board"** as names only. They were
deliberately left without prices rather than guessed at. Fill them in from the
real board:

> Fat Girl, Fat Guido, Fat Texan, Fat Ranchero, Fat Fingers, Jacki Dog,
> Quesa-Dilla, Mac Bites, Mozzarella Stix, Chicken Fries, Philly Tub, Soda

## Confirm before launch

1. **Fat Daddy price.** Two board photos disagree — one shows **$6.00**, a
   closer shot shows **$7.00**. The page uses $6.00. Check which is current.
2. **T-shirt price.** The board photo is cropped at `$28…`. The page shows
   $28.00; confirm the cents.
3. **Every other price.** Boards get repainted. Do one pass against the current
   board before this goes live.
4. **Address.** The site uses **30 Pierce St**. Grubhub also lists
   1016 W Stadium Ave — probably stale, but ask. If both are live the page
   needs a second location block.
5. **DoorDash link.** Currently a DoorDash *search* URL. Get their real store
   URL and swap it in (the third card in `#ways`, and the footer).
6. **Instagram handle.** Facebook is confirmed (`facebook.com/famousfranksPU`)
   and wired in. The main Instagram link still points at the `#famousfranksfam`
   hashtag because the canonical handle isn't confirmed — the Cary one
   (`@famousfranks.cary`) is. Swap the main handle in and add both to JSON-LD
   `sameAs`.
7. **Phone / email.** The page states outright that there isn't a phone line.
   If one exists, add it to `.facts`, the footer, and JSON-LD (`telephone`).
8. **Knight Spot hours.** Not published on the page — it links to Purdue Dining
   instead, since those hours are theirs to change, not ours.
9. **Review quote.** One real quote, attributed by source. Replace with
   verbatim quotes once you have permission, and update `aggregateRating`.
10. **Health inspection.** The 100% / no-violations claim comes from their own
    post. Confirm it's the current inspection before launch, since it's a dated
    claim.

## Assets to get from them

- **Hi-res logo.** Their real logo is now in the page (`franks-logo.jpg`, hero
  and footer) — but the source is only **450×444**, which goes soft on retina at
  hero size. The badge is capped at 380px wide to compensate. Ask for a vector
  or 1000px+ original and swap the file; nothing else needs to change.
  `favicon.svg` is still a hand-drawn FF monogram, because the full circular
  logo is unreadable at 16px — a simplified mark there is normal practice, but
  they may already have one.
- **Truck photography.** The exterior shot with the flame stripe, lit windows
  and the board. This is photo slot 01 — the page reserves space for it.
- **Food photos** — the Fat Frank, the Boat, Crackhead Fries. No slot is
  reserved for these yet; they would sit beside the board.
- **Catering photos** — they already post good ones (hot trays, salads,
  cinnamon rolls). This is photo slot 03.
- **Merch photos** — the "Go Fat or Go Home" hoodie especially.
- **An OG image** (1200×630 PNG). `og:image` is deliberately absent because SVG
  doesn't work as a social preview. Once you have truck photography, export one
  and add `<meta property="og:image" content="https://.../og.png">`.

## Design notes

Single committed dark theme, no light mode — the truck only runs between 11pm
and 3:30am, and the brand is black.

### System: Brutalism

Chosen via the `ui-ux-pro-max` skill's design-system query for this product
type, and it fits: the truck's own menu board is already brutalist. The rules
are enforced throughout —

- `border-radius: 0` **everywhere**. Zero radius declarations in the file.
- Visible `3px` borders instead of fills and blurs.
- Hard offset shadows in accent (`8px 8px 0 var(--orange)`), never blurred.
  On a dark ground a black offset shadow is invisible, so the offset carries
  the brand orange instead.
- Bold 700+ type, visible grid overlay on two sections, asymmetric blocks.
- **Zero `filter: blur()`** anywhere. All the ambient glows are gone.

One deliberate deviation: Brutalism specifies "no transitions (instant)", but
the skill's own priority-2 checklist lists `0ms` state changes as a critical
anti-pattern. Interaction feedback wins — the *visual language* is hard, but
hovers run 160ms.

### Colour — off the truck, not from the generator

The skill returned Playfair Display SC / Karla and an appetizing-red-on-cream
palette. Those are generic restaurant defaults and were **overridden**: the
real palette is sampled from the truck and the logo, which is ground truth.
(The skill's own instructions say results are recommendations, never
overrides.)

- `--orange #E8622A` the ring, and the item names on the board
- `--yellow #FFD400` the prices on the board
- `--red #D42A1C` the top of the roofline flame stripe
- `--gold #F7C825` the wordmark, `--chrome #C6CAD0` the beard
- `--sky #74B8E0` the mirrored aviators
- `--ash #ADA29A` warm grey, lightened to clear 4.5:1 on `--pitch`

The lenses run the colour system: **orange is the truck** (night, 2am), **sky
is the Knight Spot** (campus, meal swipes). That split is why Cary reads as a
distinct place rather than a footnote.

### Scales

Every size on the page comes from two fixed ramps declared in `:root` — no
ad-hoc values:

- Spacing: 8pt — `--s1` 8px through `--s8` 128px
- Type: `--t-xs` .8125rem through `--t-5xl`, `clamp()` only on the top three
- Type is Alfa Slab One (display — matches the slab lettering on their hoodie
  and board), Barlow Condensed (menu names, labels, buttons), Barlow (body)

### Motion — GSAP + ScrollTrigger

Loaded from cdnjs, pinned to 3.12.5. Presets taken from the skill's `gsap`
domain rather than invented:

| Where | Preset | Spec |
| --- | --- | --- |
| Hero | orchestrated load-in | stagger .08, `power2.out`, badge `back.out(1.4)` |
| Sections | Scroll Reveal (Standard) | `y:24`, stagger .08, `power2.out`, `top 85%` |
| Card grids | Stagger List (Standard) | `grid:'auto'`, each .06, `back.out(1.4)` |
| Menu tiles | Stagger List | each .035, **`power2.out`** — the preset's own note says overshoot reads sloppy on informational UI |
| Badge | scrub parallax | `yPercent:-14`, `scrub:1` |

Rules followed from the preset notes: **no pinning anywhere** (it warns pinning
fights native scroll on mobile), reveals capped at ~8 children, `once: true` so
nothing re-triggers, `ScrollTrigger.refresh()` on `document.fonts.ready` and
`load` because font swaps move trigger positions, and `gsap.matchMedia()`
renders the final state under `prefers-reduced-motion`.

**Crawler safety:** every start state is set by GSAP itself, never in markup.
No-JS visitors and search crawlers receive the fully rendered page — which is
the preset's explicit "don't reveal below-the-fold content as
invisible-by-default without a no-JS fallback".

### Photography

The skill's anti-pattern list for this product type is *"Low-quality imagery +
Outdated hours"*. The hand-drawn scene illustrations were deleted rather than
improved — they were exactly that. In their place are three numbered photo
slots that state which shot goes where, each with `aspect-ratio: 4/3` so
space is already reserved and dropping the real photo in causes no layout
shift. The mascot badge stays: it's a logo placeholder, not scene filler.

### Checklist state

From the skill's pre-delivery checklist: SVG icons only (no emoji),
`cursor-pointer` on every clickable, 160ms hover transitions, 4.5:1 text
contrast, `:focus-visible` at 3px with offset (WCAG 2.2 AAA focus appearance),
`prefers-reduced-motion` respected throughout, 44–48px minimum touch targets,
`inputmode` set per field, and a sticky mobile action bar with
`env(safe-area-inset-bottom)` padding compensation. Breakpoints at 480 / 640 /
768 / 1024.

Their voice is rowdy — Ass Ripper, Buffalo B!tch, Crackhead Fries, *"credit
card transactions kill small businesses"*. Item names are verbatim. Don't sand
them down.
