---
title: "Laravel to Craft CMS: What Actually Transfers, and What Doesn't"
date: 2026-09-03
draft: true
categories: ["Programming"]
tags: ["laravel", "craft-cms", "php", "programming"]
summary: "Both are PHP, both are opinionated, and that's about where the similarity stops being useful. Here's what a Laravel developer's instincts get right and wrong on a first Craft CMS project."
---

Laravel and Craft CMS solve different problems — one is an application framework, the other
is a content-first CMS with a framework underneath (Yii2, not Laravel's stack) — but enough
Laravel developers end up doing Craft work that it's worth being specific about which
instincts carry over cleanly and which will actively slow you down.

## What transfers cleanly

**Eloquent-style thinking, mostly.** Craft's `ElementQuery` API (`Entry::find()->section('blog')->all()`)
reads close enough to Eloquent's query builder that the mental model transfers almost
immediately — fluent, chainable, lazy until you call a terminal method like `.all()` or `.one()`.

**Service-container instincts.** Craft is built on Yii2, which has its own dependency
injection container and service-location patterns (`Craft::$app->getEntries()`,
`Craft::$app->getElements()`). It's not Laravel's container API, but the underlying idea —
resolve services through a central application object rather than instantiating everything
directly — is the same shape.

**Migrations.** Craft has real migration classes (`craft\db\Migration`, `safeUp()`/`safeDown()`)
that will feel immediately familiar if you've written Laravel migrations. The plugin
installation lifecycle even auto-runs a plugin's `Install` migration by convention, similar
in spirit to how Laravel discovers and runs migrations.

## What doesn't transfer, and will cost you time if you assume it does

**There's no Eloquent model per content type.** In Laravel, a `Post` model maps to a
`posts` table you control directly. In Craft, all content lives in the generic `elements`
table structure regardless of type — Entries, Assets, Users, Categories all share that
backbone, with type-specific data attached separately. You don't get to define your own
schema for a "Product" the way you'd define a Laravel model's table; you configure a Section
and Entry Type through Craft's content modeling system instead.

**No Artisan-equivalent scaffolding for content generation.** This was enough of a gap that
it's worth its own callout: Laravel's `php artisan make:factory` / `db:seed` workflow for
generating realistic demo content has no first-party Craft equivalent. If you're doing Craft
client work and miss that workflow specifically, that's a real, common pain point — [Sprout](https://plugins.craftcms.com/sprout)
brings the same `factory()->count()->create()` pattern to Craft console commands, which is
exactly the gap this section is describing.

**Project config, not `.env` + migrations, is the source of truth for structure.** Craft's
project config system tracks content structure (sections, fields, field layouts) as YAML
files meant to be committed to version control and applied across environments — it's a
different mental model from Laravel's "migrations define schema, `.env` defines
environment-specific values" split. Fighting this instead of learning it will cause real
merge-conflict pain on a team.

**Twig, not Blade.** Obvious on the surface, easy to underestimate in practice — Twig's
whitespace control, filter syntax, and template inheritance model are different enough that
muscle memory from Blade will actively mislead you on the syntax details, not just the tag
names.

## The honest takeaway

A Laravel background makes you a faster Craft developer than someone with no PHP framework
experience at all, but not because the frameworks are similar — it's because you already
have the right instincts about *where to look* for the equivalent concept, even when the
concept itself works differently underneath. Budget real time to read Craft's own docs
rather than assuming Laravel experience alone gets you there.
