# CLAUDE.md - Blog Information

## Overview
**11h.dev** is a personal notes website and blog by Alexis Blandin (alxsbn) for sharing thoughts on ideas and passions.

## Technical Stack
- **Platform**: Jekyll static site generator
- **Theme**: Minima (auto skin)
- **Markdown**: Kramdown
- **Hosting**: GitHub Pages
- **Domain**: https://11h.dev

## Blog Structure

### Content Organization
- **Posts**: Located in `_posts/` directory
- **Naming Convention**: `YYYY-MM-DD-title-slug.md`
- **Permalink Structure**: `/:year/:month/:day/:title/`

### Post Format
All posts must include front matter with:
```yaml
---
layout: post
title: "Post Title"
date: YYYY-MM-DD
categories: [category1, category2]
excerpt: "Short description for preview"
header_image: "https://images.unsplash.com/photo-XXXXX?w=1600&q=80"
header_image_alt: "Image description"
header_image_credit: "Photographer Name"
header_image_credit_url: "https://unsplash.com/@photographer"
header_image_source: "Unsplash"
header_image_source_url: "https://unsplash.com"
---
```

### Header Images
Every blog post should have a header image. The custom `_layouts/post.html` supports header images with proper attribution.

**How to add a header image:**
1. Go to [Unsplash](https://unsplash.com) and search for an image matching the article's theme
2. Copy the photo URL (format: `https://images.unsplash.com/photo-XXXXX`)
3. Add `?w=1600&q=80` for optimization
4. Copy the photographer's name and profile URL for attribution
5. Add all `header_image_*` fields to the front matter

**Supported fields:**
| Field | Required | Description |
|-------|----------|-------------|
| `header_image` | Yes | URL to the image (Unsplash or local `/assets/images/...`) |
| `header_image_alt` | Yes | Alt text for accessibility |
| `header_image_credit` | Recommended | Photographer's name |
| `header_image_credit_url` | Recommended | Photographer's profile URL |
| `header_image_source` | Optional | Source name (e.g., "Unsplash") |
| `header_image_source_url` | Optional | Source website URL |

**Local images:** Store in `/assets/images/` and reference as `/assets/images/filename.jpg`

## Author Information
- **Name**: Alexis Blandin (alxsbn)
- **Email**: alexis.blandin@gmail.com
- **GitHub**: https://github.com/alxsbn
- **LinkedIn**: https://linkedin.com/in/alexisblandin
- **Instagram**: https://www.instagram.com/alxisblandin

## Site Configuration
- **Base URL**: ""
- **URL**: https://11h.dev
- **Excerpts**: Enabled on home page
- **Social Links**: GitHub, Instagram, LinkedIn

## Navigation
Header pages:
- Home (index.md)
- About (about.md)

## Plugins
- jekyll-feed (RSS feed at /feed.xml)
- jekyll-seo-tag (SEO optimization)
- jekyll-sitemap (sitemap generation)

## Content Guidelines
- **Primary Language**: English
- **Topics**: Ideas, passions, reflections, AI, productivity, philosophy
- **Style**: Personal, thoughtful, analytical

## Git Workflow
- **Main Branch**: (default branch for production)
- **Feature Branches**: Use `claude/feature-name-sessionID` format
- Always commit with descriptive messages
- Push to feature branches before merging

## Current Articles (as of 2025-12-29)
1. "The Effort Trap: What AI Really Reveals About Work" (English)
2. "Le piège de l'effort" (French version)
3. "Laziness as a Revealer" (English, shorter version)
4. "La flemme comme révélateur" (French, shorter version)

## Notes
- Blog accepts both English and French content
- Focus on quality over quantity
- Articles explore philosophical and practical aspects of technology, work, and human behavior
