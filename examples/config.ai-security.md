# Content Scout Config (AI Security & Governance)

Beispiel-Config für einen Fokus auf AI Security, AI Agent Governance und EU AI Act.

## Basics

```yaml
language: de
frequency: daily
drafts_per_run: 2
min_signal_score: 3
output_path: 00_Daily_Notes
brand_voice_ref: 05_System/_about/brand.md
geo: DE,CH,AT
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
  - "darkreading.com"
  - "thehackernews.com"
  - "securityweek.com"
  - "owasp.org"
```

## Themen

```yaml
themes:
  - name: "AI Security"
    primary_keywords:
      - "ai security"
      - "llm security"
      - "prompt injection"
      - "jailbreak llm"
      - "ai red team"
    backup_keywords:
      - "model security"
      - "ai threat"
      - "adversarial ai"
    priority: 1

  - name: "AI Agent Governance"
    primary_keywords:
      - "ai agent governance"
      - "agent governance"
      - "autonomous agent risk"
      - "agent oversight"
      - "agent compliance"
    backup_keywords:
      - "ai accountability"
      - "agent control framework"
      - "agentic ai risk"
    priority: 1

  - name: "EU AI Act und Regulierung"
    primary_keywords:
      - "eu ai act"
      - "ai act"
      - "ai regulation"
      - "ki verordnung"
      - "ai compliance"
    backup_keywords:
      - "gpai"
      - "general purpose ai model"
      - "hochrisiko ki"
      - "nist ai rmf"
    priority: 1

  - name: "LLM Safety und Alignment"
    primary_keywords:
      - "llm safety"
      - "ai alignment"
      - "model evaluation"
      - "ai guardrails"
      - "safety benchmark"
    backup_keywords:
      - "ai evals"
      - "constitutional ai"
      - "rlhf safety"
    priority: 2

  - name: "Enterprise AI Risk"
    primary_keywords:
      - "enterprise ai risk"
      - "ai governance framework"
      - "ai risk management"
      - "third party ai risk"
    backup_keywords:
      - "shadow ai"
      - "ai supply chain"
      - "data leakage llm"
    priority: 2

  - name: "Incidents und Breaches"
    primary_keywords:
      - "ai breach"
      - "llm vulnerability"
      - "ai incident"
      - "data leak ai"
    backup_keywords:
      - "owasp llm"
      - "mitre atlas"
    priority: 2
```

## Quellen

```yaml
sources:
  # Google-Alert-Feeds eintragen (Setup-Anleitung im README)
  - type: rss
    url: "REPLACE_WITH_GOOGLE_ALERT_FEED_AI_SECURITY"
    name: "Google Alert: AI Security"

  - type: rss
    url: "REPLACE_WITH_GOOGLE_ALERT_FEED_AGENT_GOVERNANCE"
    name: "Google Alert: AI Agent Governance"

  - type: rss
    url: "REPLACE_WITH_GOOGLE_ALERT_FEED_EU_AI_ACT"
    name: "Google Alert: EU AI Act"

  # Optionale Branchenquellen
  - type: rss
    url: "https://feeds.feedburner.com/TheHackersNews"
    name: "The Hacker News"

  - type: rss
    url: "https://www.darkreading.com/rss.xml"
    name: "Dark Reading"
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

## Notiz

Vor erstem Lauf: Google-Alert-Feeds einrichten (alerts.google.com → Alert bearbeiten → "An: Feed") und die drei `REPLACE_WITH...` URLs ersetzen. Optionale Branchenquellen können bleiben oder durch eigene ersetzt werden.
