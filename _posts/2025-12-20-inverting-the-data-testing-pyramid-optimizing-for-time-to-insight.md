---
layout: post
title: "Inverting the Data Testing Pyramid: Optimizing for Time to Insight"
date: 2025-12-20
categories: data testing
excerpt: "The core issue wasn't test coverage. It was what we were optimizing for."
header_image: "https://images.unsplash.com/photo-1551288049-bebda4e38f71?w=1600&q=80"
header_image_alt: "Data visualization dashboard"
header_image_credit: "Luke Chesser"
header_image_credit_url: "https://unsplash.com/@lukechesser"
header_image_source: "Unsplash"
header_image_source_url: "https://unsplash.com"
---

At [Jolimoi](https://www.jolimoi.com) we ran 3,500 tests across 500 models with a small data team. The outcome was predictable: slower delivery, maintenance overhead, duplicated validation of logic already tested in our APIs, and no improvement in business trust.

Today we're closer to 200 tests, reduced over a few weeks.

The core issue wasn't test coverage. It was what we were optimizing for.

## The wrong question

Traditional data quality strategies focus on column-level perfection: every field validated, every rule tested, every edge case anticipated.

But the real question isn't "Is every column correct?" but "Does this system return correct answers to business questions?"

Those aren't the same thing. We rebuilt our testing strategy around that distinction, inverting the traditional testing pyramid.

## Three layers

### Layer 1: dbt tests, intentionally minimal

We enforce non-null constraints, selective uniqueness, and referential integrity on join keys only where needed to prevent failures. These tests exist to catch pipeline breaks, not validate business logic.

If a rule like `order_total = sum(line_items)` is already guaranteed and tested in our source APIs, we don't re-test it downstream. That single decision deleted thousands of redundant tests.

### Layer 2: Elementary for monitoring

This catches what we didn't think to test: volume drops, distribution shifts, schema changes. It's not about strict correctness, it's about early warning when reality diverges from expectations.

### Layer 3: benchmark corpus for business truth

We test business questions as acceptance tests, executed against our semantic layer using Databricks Genie. Each question has an expected SQL and answer, computed on frozen datasets and time-bounded metrics.

Example:

> "What was total orders in November 2025?"
> Expected: 12345

```sql
SELECT
  COUNT(DISTINCT `id_order`) AS `total_orders_november_2025`
FROM orders
WHERE YEAR(`order_date`) = 2025
```

This is where trust is validated. Not by asserting every intermediate column is perfect, but by guaranteeing stakeholders get correct and reproducible answers. When business logic changes, the benchmark is expected to break. Updating the expected answer becomes an explicit, reviewed decision. Effectively it's a data contract.

## The principle

A key principle is trusting source systems while being honest about risks. APIs being tested doesn't mean they're semantically immutable. We knowingly accept the risk of upstream contract drift and mitigate through monitoring and business-level benchmarks rather than downstream logic duplication.

This works for us because we're optimizing for insight velocity, not theoretical correctness. With a small team managing a fast-moving data platform, test maintenance cost matters. We measure success by whether stakeholders can rely on answers, not by how many tests pass.

> **Warning**: This is not a fit for regulated environments, compliance-driven reporting or organizations with strict audit requirements.

## The contract

In months our AI agent goes live for stakeholders. Our benchmark corpus is now the contract: those questions must return correct answers. If they do, the system works regardless of whether every intermediate column meets a traditional definition of perfection.

The classic testing pyramid optimizes for data correctness. We're optimizing for time to insight.
