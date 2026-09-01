# Monetization setup (do these steps yourself — they need your identity/tax info)

The site is built and ready to publish, but earning from it requires accounts I can't
create on your behalf (tax forms, identity verification, payout details). None of this
is needed to launch — traffic takes months to build regardless, so there's no rush.

## 0. Get indexed (do this first — it's the actual bottleneck, not monetization)

Right now the site earns $0 even in theory: it's live, but no search engine knows it exists
yet, and it has zero backlinks. This step matters more than affiliate signups — do it before
anything else.

1. Go to **Google Search Console** (search.google.com/search-console), sign in with your
   Google account, add property → URL prefix → `https://alanone.github.io/omarchy-notes/`.
2. Verify ownership using the **HTML tag** method (easiest for a static site — no file
   upload needed). Google gives you a line like:
   `<meta name="google-site-verification" content="XXXXXXXX" />`
   Paste that exact line to me and I'll drop it into `layouts/partials/extend_head.html` and
   push — that's the whole integration.
3. Once verified, submit the sitemap: in Search Console, go to Sitemaps → enter `sitemap.xml`
   → Submit. (The sitemap is already live and correctly generated at
   `alanone.github.io/omarchy-notes/sitemap.xml` — nothing else to build there.)
4. Optional but easy: **Bing Webmaster Tools** (bing.com/webmasters) lets you import
   directly from an already-verified Google Search Console property — one click once step 2
   is done.

Indexing typically takes days to a few weeks after submission, ranking for anything takes
months. This is the one step in the whole project with no way to speed it up other than
publishing more genuinely useful posts over time.

## 1. Affiliate links (do this first, works from day one, no traffic minimum)

Sign up for referral/affiliate programs for tools actually mentioned in the posts:

- **DigitalOcean referral program** — log into a DigitalOcean account → Settings → Referrals
  → grab your unique link. You get $25 after someone you refer spends their first $25; they
  get a $200/60-day credit. No traffic minimum, works from your first visitor. Relevant to
  the self-hosting/home-server posts.
  (Note: Hetzner's referral program stopped issuing new bonuses as of Aug 31, 2026 — dropped
  from this plan, don't bother signing up for it.)
- **Amazon Associates** — for any hardware mentioned (NAS drives, mini PCs, etc). Requires
  3 qualifying sales within 180 days of approval or the account gets closed, so don't apply
  until the site has at least a little organic traffic.

Once you have affiliate IDs, replace plain links in the posts with your affiliate links —
search each post for the relevant product/service mentions.

## 2. Ad network (add once you have consistent traffic, months out)

- **EthicalAds** (ethicalads.io) — developer-audience-focused, no huge traffic minimum,
  privacy-respecting (no tracking-based ads, fits this site's tone). Apply once the site has
  a few weeks of organic traffic and is indexed by Google.
- Google AdSense is the higher-payout alternative once traffic is meaningfully higher, but
  has stricter content/traffic requirements and clashes with the site's no-fluff tone
  (banner-heavy). Consider later, not now.

## 3. Analytics (optional, recommended)

- **Cloudflare Web Analytics** (free) — no cookie banner needed, privacy-friendly, gives you
  enough to see what's actually getting traffic. Sign up, add the site, drop the provided
  `<script>` snippet into `layouts/partials/extend_head.html` (create it if it doesn't exist).

## Realistic timeline

- Weeks 0-4: zero traffic, this is normal — search engines need time to index and trust a
  new domain.
- Months 2-6: first organic traffic if content keeps getting added and it's genuinely useful
  (this is the actual lever — publishing more good posts beats any SEO trick).
- Month 6+: affiliate clicks and ad revenue start being non-zero, typically still small
  (tens of dollars/month range) unless a post hits a high-volume, low-competition keyword.

This is the slowest-starting of the three income streams but the only one with real
compounding upside — traffic and backlinks accumulate, unlike the resource-sharing income
which is flat forever.
