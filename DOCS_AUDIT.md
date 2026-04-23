# Yuko Reviews Docs — Critical Audit

**Date:** 2026-04-23
**Scope:** Full content pass across every `.mdx` file under `quickstart.mdx`, `installation-and-setup/`, `account/`, `collect-reviews/`, `display-reviews/`, `import-reviews/`, `integrations/`, `managing-reviews/`, `questions-and-answers/`, `request-reviews/`, `review-settings/`, `support/`, `workflows/`, plus `docs.json`.

Findings are grouped by category. Each entry names a file, line reference where applicable, the offending text, and a concrete recommendation. Severity is a rough triage signal: **High** = user-visible breakage or misleading info; **Medium** = polish/clarity that erodes trust; **Low** = stylistic consistency.

---

## 1. Shopify-flavored language in a WooCommerce-only product

**No findings in content.** The docs consistently name WooCommerce where a platform is named. There is no leaked Shopify terminology (no "Liquid," "theme editor," "app block," "storefront," "myshopify," etc.).

One artifact worth noting but not worth fixing:
- `.claude/settings.local.json:7` contains a historical `rm -f … integrations/shopify.mdx …` in an allowlisted Bash command. The file is already deleted from the tree. This is a permissions allowlist entry, not user-visible docs content, so it has no effect on readers. Leave as-is unless you are pruning that allowlist.

---

## 2. Broken or stale references — **High severity**

### 2.1 `/managing-reviews/email-blacklist` — target page does not exist
Three files link to a page that was never created. The `managing-reviews/` directory contains `auto-publishing-reviews.mdx`, `index.mdx`, `replying-to-reviews.mdx`, `review-moderation.mdx`, `review-restrictions-limits.mdx`, and `verified-badges-for-reviews.mdx` — no `email-blacklist.mdx`.

- `collect-reviews/index.mdx:516` — `→ [Set up email blacklist](/managing-reviews/email-blacklist)`
- `managing-reviews/index.mdx:443` — `→ [Email blacklist setup](/managing-reviews/email-blacklist)`
- `managing-reviews/review-moderation.mdx:126` — `→ [Email blacklist setup](/managing-reviews/email-blacklist)`

**Fix:** Either write the page (feature appears real — it is cross-referenced three times) or remove the three links.

### 2.2 `/workflows/gettingStartedWithWorkflows` — camelCase path, actual file is kebab-case
- `quickstart.mdx:230` — `→ [Advanced workflows](/workflows/gettingStartedWithWorkflows)`

Actual file: `workflows/getting-started-with-workflows.mdx`. Mintlify treats these as different URLs; the link 404s.

**Fix:** Update the link to `/workflows/getting-started-with-workflows`.

### 2.3 `developer/` directory referenced by commit history but absent from tree
Recent commit `fda92fb "Move API reference docs to developer/api directory"` implies an API reference section, but `developer/` does not exist in the current working tree. Either the reference docs were removed after moving, or the move is incomplete.

**Fix:** Decide whether the API reference ships with the docs site; if yes, restore it and add to nav; if no, keep the tree as-is but note this is dead git history.

---

## 3. Frontmatter and metadata errors — **High severity** (breaks rendering/SEO)

### 3.1 `support/index.mdx:2` — stray punctuation in title
```yaml
title: "12. Support :"
```
Trailing space + colon is an accidental artifact. Renders as `12. Support :` in the left nav and `<title>` tag.

**Fix:** `title: "12. Support"`.

### 3.2 `integrations/index.mdx:2` — curly quote + dumb quote terminator
```yaml
description: "Connect Yuko with powerful third-party platforms to enhance review collection and visibility.”"
```
The description ends with `”"` — a curly right double-quote followed by a straight double-quote. This is mojibake from a paste. YAML parses the string up to the curly quote, but the final `"` is stray and may cause odd rendering in some parsers.

**Fix:** `description: "Connect Yuko with powerful third-party platforms to enhance review collection and visibility."` (one straight quote, closing cleanly).

### 3.3 `account/security-and-access.mdx:2` — editor leakage in description
```yaml
description: "Manage who can access your Yuko account with secure role and permission settings.on of your new file."
```
`...settings.on of your new file.` is leftover template text from whatever editor/tool created the file. It ships in the page description and is visible to search engines and any page-preview card.

**Fix:** `description: "Manage who can access your Yuko account with secure role and permission settings."`.

---

## 4. Filename and URL convention

### 4.1 `display-reviews/introductionToWidgets.mdx` — only camelCase file in the repo — **Medium**
Every other `.mdx` in the repo uses kebab-case. This is the lone exception. It is also the root cause of #2.2 above (the author likely followed the existing camelCase file's pattern when writing the link).

**Fix:** Rename to `display-reviews/introduction-to-widgets.mdx` and update `docs.json` and any internal links.

### 4.2 Numeric prefixes inside titles — **Low**
Every group and most page titles are prefixed with a manual dotted number (`"1. Installation & Setup"`, `"11.4 Security & Access"`, `"10.6 Zapier Integration (Upcoming)"`). Mintlify already orders nav by array position — these prefixes are redundant and brittle: any reorder forces a rename of every file's frontmatter.

**Fix (optional but worth considering):** Strip numeric prefixes; let `docs.json` order the nav. If you keep them for sidebar clarity, at minimum make them consistent — right now `quickstart.mdx` has no prefix (`"Get Started with Yuko"`), which is the correct style.

---

## 5. Terminology inconsistency — **Medium**

### 5.1 "Shop Reviews" vs "Store Reviews" vs "Site Reviews"
Even within a single file, all three labels appear for the same feature:

- `collect-reviews/collecting-shop-site-review.mdx:2` — title: `"Collecting Store Reviews "` (also note trailing space in the title)
- `collect-reviews/collecting-shop-site-review.mdx:10` — `"Shop reviews (also called Site Reviews or Store Reviews)"`
- `collect-reviews/collecting-shop-site-review.mdx:26` — header: `"Store Reviews vs Product Reviews"`
- `collect-reviews/collecting-shop-site-review.mdx:56` — `"Don't ask for shop reviews…"`
- `collect-reviews/index.mdx:110` — accordion title: `"Shop/Store Reviews (Testimonials)"`
- `collect-reviews/index.mdx:540` — step: `"Enable Shop Reviews (Optional)"`

The filename itself (`collecting-shop-site-review.mdx`) encodes *two* of the three terms.

**Fix:** Pick one canonical term (recommend **Store Reviews** since that matches the actual product UI language most closely and WooCommerce merchants typically say "store"). Use it everywhere. Add a one-line parenthetical on first mention: *"Store Reviews (sometimes called site or shop reviews)…"*. Rename the file and fix the trailing-space title.

### 5.2 "Yuko Dashboard" vs "Yuko App" vs "Yuko Admin"
The docs mostly say "Yuko Dashboard" but occasionally "Yuko App" and the URL is `app.yukoapp.com` (which is *not* `yukoreviews.com`). Confusing for a new user:
- Product brand: **Yuko Reviews**
- Marketing site: **yukoreviews.com**
- Product app URL: **app.yukoapp.com** (branded "Yuko Dashboard" in nav)

**Fix:** Either unify the product URL to a subdomain of `yukoreviews.com` (product decision) or add a one-line glossary at the top of `quickstart.mdx` explaining the Yuko Dashboard lives at `app.yukoapp.com`.

---

## 6. Placeholder / "coming soon" content — **High severity**

### 6.1 Pages titled "(Upcoming)" that contain full setup instructions
Two integration pages are contradictory — the title says the feature is not shipped, but the body gives step-by-step setup instructions. A user who clicks through will assume either (a) the title is wrong, or (b) they are about to set up a broken integration.

- `integrations/zapier.mdx:3` — `title: "10.6 Zapier Integration (Upcoming)"`
- `integrations/facebook.mdx:3` — `title: "10.8 Facebook Integration (Upcoming)"`

**Fix:** Decide which is accurate. If the integrations ship: remove "(Upcoming)." If they don't: replace the setup steps with a short "not yet available — here's what it will do" stub.

### 6.2 `display-reviews/flowing-review-badge-coming-soon.mdx` — placeholder page in nav
Title: `"4.7 Flowing Review Badge (Coming Soon)"`. The file exists only to reserve a nav slot.

**Fix:** Remove from nav until the feature ships; or replace with a short roadmap-style stub that sets expectations without pretending to be a guide.

---

## 7. Typos and small content errors — **Medium**

### 7.1 `managing-reviews/index.mdx:318` — typo in accordion title
```
<Accordion title="Filer by Published">
```
Should be **Filter by Published**. This is user-visible text in the sidebar of the Manage Reviews overview.

### 7.2 `request-reviews/review-request-emails.mdx:185–187` — empty Tip callout
```mdx
<Tip>

</Tip>
```
An empty `<Tip>` renders as a visible green callout with no content. Either it was intended to hold advice that never got written, or it's a leftover from a restructure.

**Fix:** Delete it, or fill it in.

### 7.3 Duplicate/low-value filter accordions — **Low**
`managing-reviews/index.mdx:312–360` has eight adjacent accordions ("Filter by All," "Filer by Published," "Filter by Pending," "Filter by Trash," "Filter by Spam," "Filter by With Reply:", "Filter by No Reply," "Filter by Favorites"). Each has one line of payload ("Reviews currently live on site," etc.). This adds noise without aiding discovery.

**Fix:** Collapse into a single definition list or small table.

---

## 8. Content gaps — **Medium to High**

### 8.1 No `account/index.mdx` overview page
`account/` has pages `11.1` through `11.5` but no section index. Every other major section has one. Users landing on `/account` (or the nav anchor) get whatever Mintlify defaults to.

### 8.2 No `display-reviews/index.mdx`
The section instead opens on `display-reviews/introductionToWidgets.mdx`. Same inconsistency.

### 8.3 No billing / pricing / plans page in the docs
The only mention is a single link: `review-settings/index.mdx:130` — *"Some features mentioned above are in paid plans only. Visit https://yukoreviews.com/pricing/"*. For a SaaS, users need at minimum: what's on each plan, how to upgrade, how to cancel, where the invoices live. Today this is all off-site.

### 8.4 No uninstall / data export / account deletion page
Relevant for GDPR. Especially for a plugin users install on a WordPress site — "how do I cleanly remove this" is a common question.

### 8.5 No "Migrating from X" beyond Judge.me
`import-reviews/index.mdx:184–186` says *"Coming soon: Direct integrations with Loox, Stamped.io, and Yotpo for one-click imports"* — set expectations and maybe provide a CSV-based fallback guide in the interim.

### 8.6 No developer-facing reference
No documented API auth model, rate limits, webhook signature verification (the `integrations/webhooks.mdx` page exists — verify it covers signatures). Related to finding #2.3.

---

## 9. `docs.json` / navigation structure — **Medium**

### 9.1 Group names carry manual numeric prefixes
`"1. Installation & Setup"`, `"2. Request Reviews"`, etc. See finding #4.2. In addition to being redundant, the one broken entry (`"12. Support :"`) makes the inconsistency user-visible.

### 9.2 `display-reviews` group opens on `introductionToWidgets` rather than an index
See #8.2.

### 9.3 No group-level `"default"` or overview pages for `account/` and `display-reviews/`
See #8.1 and #8.2.

---

## 10. Minor polish — **Low**

- `collect-reviews/collecting-shop-site-review.mdx:2` — title has a trailing space: `"Collecting Store Reviews "`.
- Emoji usage is sporadic. Some tip callouts use 💡/🎁; most do not. Pick one rule (e.g., "never" given the formal technical-docs tone elsewhere) and apply it.
- Image filenames in `/images/` mix snake_case (`supp.png`), camelCase, and PascalCase. Not user-visible but makes repo maintenance harder — consider a one-time rename pass.
- `collect-reviews/review-collection-form.mdx` has an `import NeedHelp from '/snippets/need-help.mdx';` line inside an Accordion body (around line 135). That's structurally invalid — imports should be at the top of the file, not inside a component. Likely a copy-paste artifact; move or remove.

---

## Recommended fix order

**Ship-blocking (fix before the next docs push):**
1. Fix three broken `email-blacklist` links (§2.1) — either write the page or remove the refs.
2. Fix the `gettingStartedWithWorkflows` typo link (§2.2).
3. Clean up the three frontmatter errors (§3.1, §3.2, §3.3) — these are showing in search results and page titles today.
4. Resolve the "Upcoming" contradictions on the Zapier and Facebook integration pages (§6.1).

**High-value polish (next pass):**
5. Standardize "Store Reviews" terminology and rename `collecting-shop-site-review.mdx` (§5.1).
6. Add `account/index.mdx` and `display-reviews/index.mdx` (§8.1, §8.2).
7. Rename `introductionToWidgets.mdx` to kebab-case and update the link in `quickstart.mdx` (§4.1).
8. Fix the "Filer" typo and empty `<Tip>` (§7.1, §7.2).

**Content investments (product decisions, not just writing):**
9. Billing/plans page (§8.3).
10. Uninstall + data export page (§8.4).
11. Developer reference — decide if `developer/` ships (§2.3, §8.6).

**Stylistic cleanup (batch whenever convenient):**
12. Drop numeric prefixes from nav (§4.2); or at minimum fix `"12. Support :"` (already in §3.1).
13. Collapse the filter-accordion wall (§7.3).
14. Pick an emoji-in-callouts rule and apply it (§10).
