# Just Dip It Tacos — Project Context for Claude Code

This file exists so a fresh Claude Code session has full context without needing
the person to re-explain everything. Read this before making changes.

## What this project is

The live website for **Just Dip It Tacos**, a Chicago quesabirria taco business
owned by **Jessica House** (jelchefe@gmail.com). Built by Russ (the person you're
likely talking to) as an LLC-run, taquero-style pop-up/catering business.

**Live domain:** justdipittacos.com
**Hosting:** GitHub Pages, served from this repo's `main` branch, root path
**Repo:** github.com/Mgamer-GHz/mgamer-ghz.github.io
**Note the repo name:** it was renamed mid-project from `Justdipittacos-site` to
`mgamer-ghz.github.io` (the special GitHub user-site naming convention). This
was NOT something Claude did — it happened on the account owner's side. It
didn't break anything; GitHub auto-redirects the old name.

## How this site came to exist (important context, not just history)

This site was originally built and iterated on as a **Squarespace mockup** —
static HTML/CSS files matching a target design, meant to eventually be rebuilt
inside Squarespace's own block editor. That approach was abandoned after
repeated, confirmed failures: Claude (via the Claude-in-Chrome browser
extension) could not reliably read or edit Squarespace's Fluid Engine canvas
content — only the outer toolbar chrome was visible to automation tools, not
actual block text/images. Manual "you click, I type" hybrid attempts also
failed (a selection/replace mismatch corrupted the hero heading text).

**The decision was made to migrate off Squarespace entirely** and self-host the
already-built HTML/CSS as a static site on GitHub Pages, which Claude has
direct, reliable file/git access to. Squarespace is still owned/paid for by the
client but is no longer the live host — DNS was pointed away from it.

**This means:** the HTML/CSS in this repo is hand-built (not exported from any
CMS), fully self-contained, and was written with real attention to matching a
specific brand system (see below). Treat it as the source of truth, not a
draft.

## File structure

```
index.html                     Homepage
about.html                     About / Our Story page
catering.html                  General catering menu + order form
just-dip-it-catering.html      Corporate catering page + order form
contact.html                   Contact page + order form
menu.html                      Real Pop-Up Menu (pulled from Square POS, see below)
CNAME                          GitHub Pages custom domain config (justdipittacos.com)
README.md                      Short repo overview

our-menus/index.html           Redirect stub -> menu.html (old Squarespace URL)
about/index.html               Redirect stub -> about.html (old Squarespace URL)
contact/index.html             Redirect stub -> contact.html (old Squarespace URL)
locations/index.html           Redirect stub -> index.html#schedule (old Squarespace URL)

*.jpg / *.webp / *.mp4         Media assets, referenced by exact filename from
                                the HTML — if you rename/move these, update every
                                reference across all 6 HTML files.
```

**No build step.** No npm, no bundler, no framework. Every page is a complete,
standalone HTML file with inline `<style>` in the `<head>`. Editing means
editing the HTML file directly and committing.

**Why the redirect stub folders exist:** Google had indexed old Squarespace
URLs (`/our-menus`, `/about`, `/contact`, `/locations`) as "sitelinks" under
search results. Those exact paths didn't exist on the new site and 404'd. The
stub folders make old URLs resolve correctly via meta-refresh + canonical tag
redirects (GitHub Pages has no native server-side redirect support).

## Design system (must stay consistent across all pages)

**Colors:**
```css
--blacktop: #1B1815;      /* near-black background */
--smoke: #2E2A26;         /* card backgrounds on dark sections */
--consomme: #C1421A;      /* red accent */
--consomme-dark: #9A3313;
--marigold: #E8A33D;      /* gold/yellow accent, primary CTA color */
--masa: #F2E8D5;          /* cream/light background */
--masa-dim: #E7DABF;
```

**Fonts (Google Fonts):**
- **Anton** — all display headings (uppercase, bold)
- **Fraunces** — body copy, serif accents
- **Libre Franklin** — default sans-serif body text
- **Space Mono** — "eyebrow" labels, buttons, prices, mono-styled UI text

**Recurring motifs:** dashed dividers, "ticket stub" badges on cards, offset
box-shadows (e.g. `box-shadow:10px 10px 0 var(--consomme)`) on photos, a wavy
SVG "drip" divider between hero and body sections, alternating dark/light
section backgrounds down each page.

**Nav/footer are duplicated per-page** (no includes/templating). If you change
nav links or footer content, you must repeat the change identically across all
6 HTML files. This is a known maintenance cost of the no-build-step approach.

## Content rules (do not violate these without asking)

- **Never invent menu items or prices.** The `menu.html` page pricing was
  pulled directly from the client's live **Square POS catalog** via the Square
  MCP connector (`Square:make_api_request` against the `catalog` service,
  `searchObjects` with `ITEM`/`ITEM_VARIATION`/`CATEGORY`/`IMAGE` types). If
  prices need updating, either re-query Square or ask the client for current
  numbers — do not guess.
- **Founding year is 2023** (was corrected from an earlier placeholder of 2019
  partway through the project — make sure this is consistent if you touch
  About/homepage copy).
- **Address:** 330 E 51st St Box 7 (front yellow box), Chicago, IL 60615 —
  but the **Google Maps "Get Directions" link intentionally uses Box 4** in
  its query string, not Box 7 — this is deliberate (Box 4 is what correctly
  resolves the map pin) and should NOT be "fixed" to match the display text.
- **Hours:** Wednesday & Saturday, 1:00–5:00 PM. **Closed dates change
  regularly** — currently Jul 18, 22, 25 — ask the client for current closed
  dates before trusting what's in the HTML.
- **"Non-GMO" language was deliberately removed** sitewide per client request
  — copy now just says "locally sourced." Don't re-add it.
- **Personal Chef / on-site tent+grill language was deliberately removed from
  Corporate Catering copy** — corporate catering is now framed as trays
  delivered/served in-office, NOT a tent-and-grill setup. That distinction
  (tent+grill) is reserved specifically for the "Outdoor Taquero Service"
  section/offering, which is a different service.
- **Never expose Jessica's Calendly link publicly on a page a random visitor
  could reach.** It's intentionally scoped to appear ONLY on the post-submit
  "thank you" state of the order forms, after someone has already submitted
  an inquiry — not embedded anywhere on the public-facing page itself.

## Third-party integrations

### Web3Forms (order form backend)
- Free tier, unlimited submissions, no dashboard login required
- Access key (already embedded client-side in all 3 forms — this is by
  design, Web3Forms keys are meant to be public/client-side, not secret):
  `0e1e9146-18d9-4a46-b8d4-0fedaa280040`
- Registered to domain `justdipittacos.com` and to Jessica's email
  (jelchefe@gmail.com) — submissions land in her inbox
- Forms submit via `fetch()` to `https://api.web3forms.com/submit`, JSON
  `Accept` header, `FormData` body. See the `<script>` block at the bottom of
  `catering.html`, `just-dip-it-catering.html`, or `contact.html` for the
  exact implementation — all three are near-identical.
- Each form has a distinct hidden `subject` field so Jessica can tell which
  page an inquiry came from.
- Has a honeypot field (`name="botcheck"`, hidden) for basic spam filtering.
- **Known limitation:** the free tier has no autoresponder (auto-email-reply
  to the customer) — that's a paid Web3Forms feature. The current
  "confirmation" is (a) an inline on-page "Thanks!" message, and (b) the
  Calendly widget appearing immediately after — no confirmation email is sent
  to the customer.

### Calendly (post-submit scheduling)
- Link: `https://calendly.com/justdipittacos/30min`
- **Must use this exact event-type URL, not the bare profile link**
  (`https://calendly.com/justdipittacos`) — prefill (auto-filling name/email)
  is documented/confirmed to only work reliably with direct event-type URLs,
  not profile-level links. This was empirically confirmed during build (profile
  link showed empty fields; event-type link correctly prefilled).
- Implementation: `Calendly.initInlineWidget()` JS API, called manually inside
  the form's success handler (not the auto-scanning `class="calendly-inline-widget"`
  method) so it only renders after a successful submission.
- `prefill: { name, email }` is populated from values captured off the form
  fields *before* the form's innerHTML is replaced with the thank-you state.
- Widget script (`https://assets.calendly.com/assets/external/widget.js`) and
  stylesheet are loaded in `<head>` with `defer` on all 3 form pages.
- A plain fallback link (`Open Scheduling Page In A New Tab`) also exists
  alongside the embedded widget, carrying the same prefill via URL query
  params, in case the embed fails to load for any reason.

### Square (POS / menu source of truth)
- Connected via the Square MCP connector, `make_api_request` tool.
- Used **once**, read-only, to pull real catalog data for `menu.html`. Not
  wired into the site as a live/dynamic integration — `menu.html` is a static
  snapshot of what was in Square's catalog at build time. If the client's
  Square menu changes, `menu.html` needs to be manually re-synced (re-query
  Square, update the HTML) — it does not auto-update.
- Square's catalog had **duplicate category entries** for "Pop-Up Menu" (3
  different category IDs, same/similar name) — when re-querying, check for
  this and merge/dedupe rather than assuming one canonical category ID.
- Square's catalog had **almost no product images** attached (only 3 images
  existed across the entire catalog when checked) — most menu items on
  `menu.html` are text-only, not a Claude oversight.

### GitHub Pages / DNS
- Custom domain configured via the `CNAME` file (contains just
  `justdipittacos.com`) plus GitHub Pages' "Custom domain" setting.
- DNS is managed through Squarespace's domain panel (client still owns/pays
  for the domain registration there, just not the hosting) — 4 A records
  pointing at GitHub's IPs (185.199.108.153 / .109.153 / .110.153 / .111.153)
  plus a `www` CNAME to `mgamer-ghz.github.io`.
- HTTPS is enforced; SSL cert is auto-managed by GitHub, covers both apex and
  `www`.
- **If Pages ever shows a 404 or "DNS check in progress" that seems stuck,**
  the most common causes seen during this build were: (1) local DNS caching
  on the client's end (not a real problem, just propagation lag — test from a
  different network to confirm), or (2) the `CNAME` file was accidentally
  removed from the repo (this happened once, when it was intentionally
  removed to preview the site at the raw `.github.io` URL and not restored
  afterward — always double check the CNAME file exists before assuming a
  deeper problem).

## Credentials — what's NOT in this repo (intentionally)

The **GitHub Personal Access Token** used to push commits during this project
is NOT stored anywhere in this repo, and should never be committed. It was a
fine-grained PAT scoped narrowly to `Contents: Read and write` on this one
repo (later access needs, like toggling Pages settings, required the human to
do it manually via GitHub's UI since the token didn't have `Pages` scope).

If you're Claude Code picking this up fresh: you'll need the person to either
(a) have you authenticated via their own local git credentials/SSH already, or
(b) supply a fresh token if you need to push on their behalf. Don't ask them to
paste a token into chat if you have a more secure local credential flow
available (Claude Code typically runs with the person's own local git config,
which is safer than a pasted token).

## Known open items / things not yet done

- **No confirmation email to customers** (Web3Forms free tier limitation,
  discussed but not resolved — see above).
- **Menu page is a static snapshot**, not live-synced to Square. Will drift
  out of date if Square's real menu changes.
- **No analytics** were set up during this project (not discussed/requested).
- **Google Search Console** — not confirmed whether it's set up for this
  domain; recommended during the redirect-stub work but not verified.
- **Client still owns a Squarespace subscription** for this same domain, no
  longer used for hosting. Not cancelled as of this writing — that's the
  client's call, not something to prompt unless asked.
- The visual design intentionally does **not** exist inside Squarespace
  anymore in any current/maintained form — an earlier attempt to reskin
  Squarespace via Custom CSS was abandoned (Squarespace's native section
  background color picker didn't accept hex input, and the gap versus the
  custom HTML/CSS build was judged too large to be worth continuing).
