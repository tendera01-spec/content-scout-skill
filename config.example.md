# Content Scout Config (Template)

Diese Datei steuert was, wo und wie gescoutet wird. Kopiere sie nach `config.md` und passe sie an. Der Skill liest immer `config.md`, nicht diese Datei.

---

## Basics

```yaml
language: de              # de | en
frequency: daily          # daily | weekly
drafts_per_run: 2         # wie viele Post-Drafts pro Lauf
min_signal_score: 3       # 0 bis 4, ab welchem Score ein Item gedraftet wird

# PFLICHT ANPASSEN: Wohin die täglichen Drafts geschrieben werden (relativ zum Vault/Projekt-Root)
output_path: TODO_PATH_TO_OUTPUT_FOLDER       # Beispiel Obsidian: 00_Daily_Notes
                                              # Beispiel Claude Code: ./content-drafts

# PFLICHT ANPASSEN: Datei mit deinem Schreibstil (Tone, Verbots-Wörter, Beispiele)
# Wenn keine vorhanden: neue Datei anlegen oder Pfad auf einen leeren Default setzen
brand_voice_ref: TODO_PATH_TO_BRAND_VOICE_FILE  # Beispiel: brand-voice.md im Repo-Root

geo: DE                   # für Google Trends, z.B. DE, CH, US, AT (mehrere mit Komma)
```

## Keyword-Quellen (Cascade)

Welche Tools für SEO/Keyword-Recherche genutzt werden sollen. Skill prüft pro Run welche authentifiziert sind und nutzt die beste verfügbare Stufe. Reihenfolge nach Datenqualität:

```yaml
keyword_sources:
  ahrefs: true            # hartes Volume + Difficulty (paid, MCP-Auth nötig)
  similarweb: true        # Branchen-Traffic + Konkurrenz-Keywords (paid, MCP-Auth nötig)
  pytrends: true          # Google Trends, Rising Queries (free)
  perplexity: true        # qualitative Trend-Signale (free MCP)
```

Stell false oder weglassen wenn ein Tool nicht genutzt werden soll. Config-Backup-Keywords laufen immer als Floor.

## Konkurrenz-Domains (optional, für SimilarWeb)

```yaml
competitor_domains:
  - "example-blog.com"
  - "branchenportal.de"
```

Wird nur genutzt wenn `keyword_sources.similarweb: true` und MCP authentifiziert ist. Skill zieht dann Top-Keywords dieser Domains als Themen-Discovery.

## Themen

Mehrere Themen-Cluster. Jeder Cluster hat Primary-Keywords (für Match) und Backup-Keywords (für SEO-Fallback wenn pytrends ausfällt).

```yaml
themes:
  - name: "Theme 1 (z.B. AI Security)"
    primary_keywords:
      - "ai security"
      - "llm security"
      - "prompt injection"
    backup_keywords:
      - "ai red team"
      - "model security"
    priority: 1

  - name: "Theme 2 (z.B. Solo Founder mit AI)"
    primary_keywords:
      - "solo founder"
      - "one person company"
      - "ai operating model"
    backup_keywords:
      - "indie hacker"
      - "solopreneur ai"
    priority: 2
```

## Quellen

```yaml
sources:
  # Google Alerts als RSS (https://www.google.de/alerts -> Bearbeiten -> "An: Feed")
  - type: rss
    url: "https://www.google.com/alerts/feeds/XXXXXXXX/YYYYYYYY"
    name: "Google Alert: AI Security"

  - type: rss
    url: "https://www.google.com/alerts/feeds/XXXXXXXX/ZZZZZZZZ"
    name: "Google Alert: Solo Founder"

  # Andere RSS-Feeds
  - type: rss
    url: "https://example.com/feed"
    name: "Branchenblog X"

  # Web-Seite (wird gescraped, kein Feed)
  - type: web
    url: "https://www.example.com/news"
    name: "Branchenportal Y"
```

## Channels

```yaml
channels:
  - linkedin
  - x
  # - blog
  # - newsletter
```

## Hashtag-Regeln

```yaml
hashtags:
  linkedin: true            # bis 5
  x: true                   # bis 2
  blog: false
  newsletter: false
```

## Format-Notizen

- `output_path` ist relativ zur Vault-Root
- `brand_voice_ref` muss eine Datei sein die Tone, Sprache, Verbots-Wörter, Style-Beispiele enthält
- RSS-URLs aus Google Alerts: im Alert-Setup auf "An: Feed" stellen, dann den Feed-Link kopieren
- Wenn `geo` mehrere Märkte sind: Comma-separated, z.B. "DE,CH"

## Setup-Schritte für neuen User

1. Diese Datei kopieren nach `config.md` im selben Ordner
2. Brand-Voice-Datei anlegen oder Pfad anpassen
3. Mindestens ein Theme und eine Quelle eintragen
4. Output-Pfad prüfen (Ordner muss existieren)
5. Skill starten mit "content scout" im Chat
