---
title: "API Versioning Strategies That Actually Work in Production"
date: 2026-09-03
draft: false
categories: ["Programming"]
tags: ["api", "rest", "programming", "backend"]
summary: "Every API eventually needs to change in a way that breaks someone. The question isn't whether to version — it's which strategy you can actually maintain two years in."
---

Every API tutorial covers versioning in a paragraph. Every real API that's survived past its
first year has a versioning story that's messier than the tutorial suggested, because the
actual hard part isn't picking a scheme — it's living with it once real clients depend on it.

## The three real options

**URL versioning** (`/v1/users`, `/v2/users`) — the most common approach, and for good
reason: it's visible, cacheable, and trivially easy for a client to understand and route on.
The honest downside is that it makes "the resource" and "the version of the resource" look
like they're the same thing in the URL, which they conceptually aren't — `/v1/users/42` and
`/v2/users/42` are the same user, but nothing about the URL structure communicates that.

**Header versioning** (`Accept: application/vnd.yourapi.v2+json`, or a custom
`X-API-Version` header) — keeps the URL clean and correctly signals that versioning is a
representation concern, not a resource-identity concern. The real cost: it's invisible in
browser address bars, harder to test with a quick curl command from memory, and easy for
API consumers to simply forget to set — meaning your "default" version behavior needs to be
deliberately chosen, not accidental.

**No versioning, only additive changes** — the strategy of never introducing a breaking
change at all: only ever add new optional fields, never remove or rename existing ones,
never change a field's meaning. This is the strategy that actually requires the most
discipline of the three, but it's the only one that avoids the "how long do we support v1"
problem entirely, because there's only ever one version. Stripe is the most commonly cited
real-world example of running this model successfully at scale for years.

## The part nobody puts in the tutorial: deprecation

Picking a versioning scheme is the easy 10%. The actual hard part is what happens 18 months
later when you want to retire v1 and half your integrations still use it. A few things that
actually work in practice:

- **Put a deprecation date in the response itself**, not just documentation nobody reads —
  a `Deprecation` or `Sunset` HTTP header (both are real, standardized headers for exactly
  this) that API monitoring tools and thoughtful client developers will actually notice.
- **Log which version each request uses**, from day one, before you have any reason to care
  yet. When you eventually want to deprecate v1, "nobody's called that in 90 days" is a
  decision you can only make if you were already logging it.
- **Never set a hard cutoff date until usage is genuinely near zero.** A grace period that
  gets extended once because "one enterprise client needs three more months" is normal and
  fine. A grace period you refuse to extend because you already announced it publicly is how
  you lose that client's trust in every future announcement you make.

## The honest recommendation

For a new API with no existing constraints: **prefer additive-only changes for as long as
you possibly can**, and reach for URL versioning as the fallback the first time you
genuinely need a breaking change (renaming a field, changing a response shape, changing
required-vs-optional). It's the combination that's easiest for API consumers to reason
about, and the one most real production APIs converge on even when they started somewhere
else.
