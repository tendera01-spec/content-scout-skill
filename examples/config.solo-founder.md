# Content Scout Config (Solo Founder mit AI)

Beispiel-Config für einen Fokus auf Solo Founder, AI in der Kreativbranche und Creative Operations.

## Basics

```yaml
language: de
frequency: daily
drafts_per_run: 2
min_signal_score: 3
output_path: 00_Daily_Notes
brand_voice_ref: 05_System/_about/brand.md
geo: DE,CH
```

## Keyword-Quellen

```yaml
keyword_sources:
  ahrefs: true
  similarweb: true
  pytrends: true
  perplexity: true
```

## Konkurrenz-Domains (für SimilarWeb)

```yaml
competitor_domains:
  - "smashingmagazine.com"
  - "nngroup.com"
  - "uxdesign.cc"
  - "uxcollective.com"
```

## Themen

```yaml
themes:
  - name: "AI in Design und Kreativbranche"
    primary_keywords:
      - "ai design"
      - "generative design"
      - "ai ux"
      - "ai creative"
      - "design ai tools"
    backup_keywords:
      - "ai in figma"
      - "ai workflow design"
      - "creative ai studie"
    priority: 1

  - name: "Solo Founder mit AI"
    primary_keywords:
      - "solo founder"
      - "one person company"
      - "ai operating model"
      - "solopreneur"
      - "indie hacker ai"
    backup_keywords:
      - "ai automation solo"
      - "one man company"
    priority: 1

  - name: "Creative Operations"
    primary_keywords:
      - "creative operations"
      - "creative ops"
      - "design ops"
      - "creative workflow"
    backup_keywords:
      - "marketing operations"
      - "creative automation"
    priority: 2

  - name: "UX Strategie und Design Systems"
    primary_keywords:
      - "design system"
      - "ux strategy"
      - "experience strategy"
      - "service design"
    backup_keywords:
      - "design tokens"
      - "design system governance"
    priority: 2
```

## Quellen

```yaml
sources:
  - type: rss
    url: "REPLACE_WITH_GOOGLE_ALERT_FEED_1"
    name: "Google Alert: AI Design"

  - type: rss
    url: "REPLACE_WITH_GOOGLE_ALERT_FEED_2"
    name: "Google Alert: Solo Founder"
```

## Channels

```yaml
channels:
  - linkedin
  - x
```

## Hashtag-Regeln

```yaml
hashtags:
  linkedin: true
  x: true
```
