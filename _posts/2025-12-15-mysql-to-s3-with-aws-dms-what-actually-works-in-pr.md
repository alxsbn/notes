---
layout: post
title: "MySQL to S3 with AWS DMS: What actually works in production"
date: 2025-12-15
categories: [data, work, tech]
excerpt: "I don't like sharing experience reports on specific software. These articles age poorly. But on AWS DMS to S3 we've suffered enough at…"
lang: en
header_image: "https://images.unsplash.com/photo-1558494949-ef010cbdcc31?w=1600&q=80"
header_image_alt: "Server room with blue lights"
header_image_credit: "Taylor Vick"
header_image_credit_url: "https://unsplash.com/@tvick"
header_image_source: "Unsplash"
header_image_source_url: "https://unsplash.com"
---

I don’t like sharing experience reports on specific software. These articles age poorly. But on AWS DMS to S3 we’ve suffered enough at [Jolimoi](https://www.jolimoi.com) that keeping it to ourselves would be criminal.

**Warning:** What follows is only valid in our context. AWS RDS Aurora MySQL > DMS > S3 > Databricks Autoloader (micro-batch every 30 minutes).


## 1. Partitioning: keep it simple


Hive-style, `YYYYMMDD` partition, period. No unnecessary complexity. It works for us because we don't do query pruning directly on S3 since we ingest everything via Autoloader to Delta Lake.

The real benefit: backfills and migrations become trivial. Adapt to your volume and access patterns, but start simple.


## 2. Split DMS tasks strategically


Don’t map 1 source = 1 task. Split by table type (volume, churn rate, criticality). This gives you independent full reloads and granular tuning.

But be careful: we only do this on isolated tables. If table A references table B and they’re in separate tasks, you risk temporal inconsistencies.

Also, we handle schema drifts on the Autoloader side with automatic schema inference.


## 3. Premigration assessments: skip them


When your target is S3, I think it’s a waste of time. DMS pushes unstructured Parquet, there’s no relational schema to validate.

We just do baseline checks (row counts, a few checksums). Pushing to S3 costs so little that you can iterate quickly without drowning in validations that don’t make sense in this context.


## 4. DMS Serverless: we rolled back


We tested it several times over 3 to 6 months. More expensive, less performant, unpredictable scaling.

We rolled back every time. AWS constantly improves the product, but today pricing-wise, classic instances remain more predictable and economical.


## 5. Transaction metadata is non-negotiable


Add a transaction ID (`AR_H_CHANGE_SEQ` for MySQL), the `Op` (INSERT/UPDATE/DELETE), and the `TIMESTAMP`. DMS can truncate millisecond precision.

Without this, you can't reliably order concurrent operations. It looks like a detail until the day it breaks your consistency guarantees.


## 6. Binlog retention & instance sizing


**Source side**: increase your binlog retention. Seven days minimum for us. We lost events once because of a binlog that expired too quickly. We don’t skimp anymore since then.

**DMS side**: spin up a large instance for the full load, run the import, then downsize before switching to continuous CDC. This saved us thousands of euros. However, never undersize instance storage.


## 7. Small files: the real enemy


Even with Parquet. We target 100–200 MB per file. Compaction happens automatically on Databricks side (Auto Optimize + Z-Ordering), after ingestion into Delta Lake.

We tune DMS commit/flush accordingly. Loading cost from S3 is negligible, and we normally only load once .


## Conclusion


AWS DMS to S3 is not a “set and forget” solution. What makes the difference is task granularity on isolated tables, complete transaction metadata, smart sizing and a real obsession with operational details.

And above all, your downstream architecture matters as much as DMS. We never query directly on S3 since everything goes through Autoloader to Delta Lake, then dbt. Parquet on S3 is for us just an ingestion buffer.

We catch DMS errors and monitor via a custom agent to Slack. The only really nasty bug we encountered: an `ALTER TYPE` on a MySQL ENUM that shuffled values. Detected quickly, fixed and re-full load.

Today, we ingest hundreds of TBs with almost zero maintenance. But it took 2 years to get there. Adapt to your context, don’t blindly copy!