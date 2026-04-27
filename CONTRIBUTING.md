# Contributing to Yuko Reviews Docs

This site is built on [Mintlify](https://mintlify.com). All content lives in
`.mdx` files; navigation is defined in `docs.json`.

## Running locally

```bash
npm install -g mintlify
mintlify dev
```

The preview runs at `http://localhost:3000` and reloads on save.

## Directory layout

```
.
├── docs.json                  # Mintlify config and nav
├── quickstart.mdx             # Top-level landing page
├── installation/              # Plugin install and store connection
├── import-reviews/            # Migrating from WooCommerce native / Judge.me
├── collect-reviews/           # Request emails, reminders, on-site forms
├── display-reviews/           # Widgets, branding, display controls
├── managing-reviews/          # Moderation, blacklists, syndication
├── questions-and-answers/     # Q&A feature
├── workflows/                 # Automation builder
├── integrations/              # Third-party connectors
├── account/                   # Account, billing, team, multi-store
├── developer/                 # Hooks, shortcodes, CSS, REST API
├── troubleshooting/           # Common issues and fixes
├── support/                   # Contact info
├── snippets/                  # Reusable content (see below)
└── images/                    # All screenshots and diagrams
```

## Writing style

Yuko docs are **task-oriented and beginner-friendly**. A merchant should be able
to land on a page and accomplish the task without reading the rest of the site.

### Voice

- Second person ("you"). Never first person ("we" is okay for the Yuko team).
- Present tense.
- Short sentences. Break long paragraphs with headings, bullets, or Mintlify components.
- No marketing fluff. Every sentence should help the reader do something.

### Page structure

Most feature pages follow this shape:

1. **Frontmatter** — `title`, `description` (required), optional `icon`.
2. **One-paragraph intro** — what the feature is and who it's for.
3. **Dashboard path** — use the `<DashboardPath>` snippet.
4. **Main content** — split with `<Steps>`, `<AccordionGroup>`, `<Tabs>`, or `<CardGroup>`.
5. **Related links** or "Next Steps" at the bottom.
6. **`<NeedHelp />`** snippet as the final section.

### Frontmatter

```yaml
---
title: "Short, descriptive, no numeric prefix"
description: "One sentence that previews the page for search engines."
icon: "optional-heroicon-name"
---
```

**Don't** prefix titles with section numbers (`1.2 Foo`). Mintlify already orders
them by array position in `docs.json`.

### Components we use

| Component | When to use |
| --- | --- |
| `<Steps>` | Sequential setup instructions |
| `<AccordionGroup>` + `<Accordion>` | FAQs, optional details, troubleshooting |
| `<Tabs>` + `<Tab>` | Parallel alternatives (e.g. "Add manually" vs "Upload CSV") |
| `<CardGroup>` + `<Card>` | Links to related pages, feature comparisons |
| `<Info>` / `<Tip>` / `<Warning>` / `<Note>` / `<Check>` | Highlighted callouts |
| `<Columns>` | Side-by-side layout inside a larger section |

### Images

- Store in `/images/` using lowercase-kebab-case names
- Always include alt text: `![Descriptive alt](/images/filename.png)`
- Prefer PNG for UI screenshots, SVG for diagrams
- Target ~1600px wide for dashboard screenshots; Mintlify handles optimization

## Reusable snippets

Use snippets whenever the same content appears in 3+ pages. Snippets live in
`/snippets/`. We already have:

- `need-help.mdx` — the standard "Need help?" card grid, used at the end of most pages
- `dashboard-path.mdx` — the `<DashboardPath path="..." />` component for navigation breadcrumbs
- `plan-availability.mdx` — `<PlanAvailability plans="Advanced and Growth" />` for plan-gated features
- `coming-soon.mdx` — `<ComingSoon feature="..." />` callout
- `woocommerce-requirements.mdx` — the standard WC/WP/PHP version checklist
- `widget-basics.mdx` — the "every widget has layout/style/colors/preferences/CSS/code" explainer

**To use a snippet**, import it at the top of your `.mdx`:

```mdx
import NeedHelp from '/snippets/need-help.mdx';
import { DashboardPath } from '/snippets/dashboard-path.mdx';

<DashboardPath path="Reviews → Emails → Review Request" />

...

<NeedHelp />
```

**Create a new snippet** when the same block of text appears in three or more
pages. Pass any variable bits as props (`{ feature = "default" }`).

## Internal links

Link to pages using their URL path (without `.mdx`):

```mdx
See the [Email Blacklist](/managing-reviews/email-blacklist) page.
```

Before merging, grep for any path fragments you changed to make sure nothing
links at the old location:

```bash
grep -r "/old-path-fragment" --include="*.mdx"
```

## Platform scope

The Yuko SaaS supports both WooCommerce and Shopify. **This documentation site
covers WooCommerce only.** When you reference a feature that is Shopify-only
(Loyalty, Referral, VIP Tiers, Membership), either omit it or flag the limitation
clearly. Never use Shopify-specific terminology (Liquid, theme editor, app block,
myshopify) in this repo.

## Pull requests

1. Create a feature branch off `main`
2. Write or edit `.mdx` files and update `docs.json` nav if adding a page
3. Run `mintlify dev` locally and click through your changes
4. Open a PR with a short description of what changed and why
5. Screenshots welcome in the PR body if you're changing the visual result

## Style checks

Before pushing:

- [ ] Frontmatter `title` and `description` present and free of stray quotes
- [ ] New pages registered in `docs.json`
- [ ] No broken internal links (`grep -rn '](/' --include='*.mdx'` and spot-check)
- [ ] Screenshots have alt text
- [ ] Page uses `<NeedHelp />` at the end
- [ ] No Shopify-only features documented as available to WooCommerce users
