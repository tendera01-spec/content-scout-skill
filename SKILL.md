---
name: content-scout
description: |
  Täglicher (oder wöchentlicher) Content-Scout. Liest RSS-Feeds (z.B. Google Alerts), Branchenquellen und Trend-Signale. Filtert nach konfigurierten Themen, prüft SEO-Relevanz via Keyword/Trend-Check und draftet fertige Post-Entwürfe (LinkedIn, X, Blog) im Brand-Voice des Users. Themen-agnostisch über `config.md`. Triggert bei: "content scout", "neues posten", "content drafts", "was kann ich posten", "trend check".
argument-hint: "[fokus-thema, optional]"
---

# Content Scout (v2, generisch)

## Zweck

Aus Marktsignalen (RSS, Web, Trends) tägliche oder wöchentliche Post-Drafts produzieren, die zur Positionierung des Users passen und SEO-relevant sind. Themen, Quellen, Output, Brand-Voice und Frequenz kommen aus `config.md` neben diesem Skill. Der Skill bleibt portabel.

## Phase 0: Setup laden

1. `config.md` aus demselben Ordner laden (`05_System/skills/content-scout/config.md`).
   - Wenn nicht vorhanden: User auf `config.example.md` hinweisen, abbrechen.
2. Master-Rules laden, sofern im Vault vorhanden (`05_System/_about/master-rules.md`). Wenn nicht vorhanden: weiter.
3. Brand-Referenz aus Config laden (z.B. `brand.md`).

Aus Config lesen:
- `themes`: Liste der Kernthemen mit Keywords
- `sources`: RSS-Feeds, Websites, optionale Crawl-Targets
- `output_path`: Wohin geschrieben wird
- `frequency`: daily | weekly
- `drafts_per_run`: Anzahl Post-Drafts pro Lauf
- `channels`: Liste von Kanälen (linkedin, x, blog, newsletter)
- `brand_voice_ref`: Pfad zur Brand-Datei
- `language`: de | en
- `min_signal_score`: Schwelle 0 bis 4
- `keyword_sources`: welche Tools für Keyword-Recherche aktiviert sind (ahrefs, similarweb, pytrends, perplexity)
- `competitor_domains`: optionale Domain-Liste für SimilarWeb-Benchmarking

## Phase 1: Quellen scannen

Für jede Quelle in `sources`:

1. **RSS-Feeds (Priorität 1)**
   - Tool: `firecrawl_scrape` mit URL des Feeds, formats: ["markdown"]
   - Fallback: `web_fetch`
   - Items der letzten N Stunden (daily = 24h, weekly = 168h)
   - Extrahiere: Titel, URL, Veröffentlichungsdatum, Snippet

2. **Web-Quellen (Priorität 2)**
   - Tool: `firecrawl_scrape` einzelne Seiten, oder `firecrawl_crawl` für Sektionen
   - Nur wenn in Config explizit gelistet

3. **Optional: Perplexity Reasoning**
   - Tool: `perplexity-mcp` mit Fokus-Thema, wenn User Argument übergeben hat
   - Liefert kuratierte aktuelle Quellen

Cap: max 50 Roh-Items pro Lauf. Mehr wird gestaucht, nicht erweitert.

## Phase 2: Themen-Match (Relevanz-Filter)

Pro Item:
- Match gegen `themes[].keywords` (case-insensitive, Whole-Word + Stemming light)
- Score 0 bis 4 nach Kriterien:
  1. Themen-Match in Titel oder Snippet
  2. Aktualität (innerhalb der Frequenz-Fenster)
  3. Substanz (Daten, Studie, Case, konkrete Zahl, namhafte Quelle, nicht reine Meinung)
  4. Actionability (kann daraus ein Post werden mit eigenem Take)

Nur Items mit Score >= `min_signal_score` (default 3) weiter.

## Phase 3: SEO und Keyword-Check (Cascade)

Für die Top N Items (N = `drafts_per_run` x 2, für Auswahl). Skill nutzt die beste verfügbare Datenquelle aus der Cascade. Bei jeder Stufe wird geprüft ob das Tool authentifiziert ist (sonst skip).

### Cascade-Reihenfolge (beste Daten zuerst)

**Stufe 1: Ahrefs** (wenn `keyword_sources.ahrefs: true` und MCP authentifiziert)
- Tool: `mcp__plugin_marketing_ahrefs__*`
- Pro Themen-Cluster: Keyword Volume, Difficulty, Related Keywords mit Volumen
- Output: harte Suchvolumen-Zahlen, SEO-Difficulty pro Keyword
- Ergebnis: priorisierte Keyword-Liste mit Volume und Difficulty Score

**Stufe 2: SimilarWeb** (wenn `keyword_sources.similarweb: true` und MCP authentifiziert)
- Tool: `mcp__plugin_marketing_similarweb__*`
- Pro Domain in `competitor_domains` (oder Branchen-Auto-Discovery): Top Keywords der Konkurrenz, Traffic-Trends, Branchen-Benchmarks
- Output: Themen die in der Branche aktuell Traffic ziehen
- Ergebnis: Themen-Discovery + Konkurrenz-Keywords

**Stufe 3: pytrends (Google Trends)** (wenn `keyword_sources.pytrends: true`)
- Bash: `pip install pytrends --break-system-packages` (einmalig)
- Für jeden Themen-Cluster: `interest_over_time` der letzten 30 Tage, `related_queries`, `trending_searches` im Zielmarkt (`geo` aus Config)
- Output: Rising-Queries, Top-Queries pro Cluster, Trend-Richtung

**Stufe 4: Perplexity** (wenn `keyword_sources.perplexity: true` oder als Fallback)
- Tool: `mcp__perplexity-mcp__search` mit Query "what are people searching about [topic] right now [Jahr] in [geo]"
- Output: qualitative Trending-Signale, kuratierte Quellen
- Auch nützlich für Reasoning warum ein Thema gerade hochkommt

**Stufe 5: Config-Backup-Keywords**
- Aus jedem Theme die `backup_keywords` als Floor
- Wird immer als Sicherheit hinzugefügt, auch wenn andere Stufen Daten liefern

### Datenfusion

Pro Themen-Cluster:
1. Sammle alle Keywords aus aktiven Stufen
2. Dedupliziere (case-insensitive)
3. Wenn Ahrefs Daten hat: sortiere nach `volume / max(difficulty, 1)` (Sweet Spot)
4. Wenn nur Trends/Perplexity: sortiere nach "Rising"-Flag, dann Trend-Slope
5. Cap: max 20 Keywords pro Cluster für weitere Verarbeitung

### Keyword-Set pro Draft zusammenbauen

- 1 Primary Keyword (höchstes Volume bei akzeptabler Difficulty, oder stärkstes Rising-Signal)
- 2 bis 3 Secondary Keywords (Related Queries oder mittleres Volumen)
- 3 bis 5 Hashtags (für LinkedIn/X), wenn Config Hashtags zulässt
- Nur Keywords aufnehmen die echtes Suchvolumen, Rising-Signal oder qualitative Bestätigung haben
- Im Output markieren welche Quelle das Keyword geliefert hat (Ahrefs/SimilarWeb/Trends/Perplexity/Config)

### Fehlerbehandlung Cascade

- Tool nicht auth oder Error: Stufe skip, weiter mit nächster
- Alle Stufen 1 bis 4 fehlgeschlagen: Stufe 5 (Config-Backup) als alleinige Quelle, im Output prominent markieren
- Log welche Stufen genutzt wurden in `scout-runs.jsonl`

## Phase 4: Draft-Erstellung

Aus Top-Items die besten `drafts_per_run` auswählen. Für jeden Draft:

1. Lade Brand-Voice (`brand_voice_ref`).
2. Pro Channel in `channels` einen Draft erzeugen.
3. Struktur Draft:
   - Hook (1 Satz, scharf, ohne Smalltalk)
   - Substanz (2 bis 4 Sätze: was, warum, was bedeutet das)
   - Take (1 Satz, eigene Sicht, kein Hedging)
   - CTA oder Frage (1 Satz, optional)
   - Keywords organisch eingebaut, nicht angeklebt
   - Hashtags am Ende (nur wenn Channel und Config das wollen)

4. Channel-spezifische Constraints:
   - **X**: max 280 Zeichen, kein Hashtag-Spam, max 2 Hashtags
   - **LinkedIn**: 800 bis 1300 Zeichen optimal, Zeilenumbrüche für Lesbarkeit, max 5 Hashtags, erste Zeile = Hook (Above-the-Fold)
   - **Blog**: 500 bis 1500 Wörter, H2-Struktur, Keyword in Title und ersten 100 Wörtern
   - **Newsletter**: persönlicher Ton, kein Hashtag

5. Brand-Voice-Regeln einhalten (aus `brand_voice_ref`): keine Em-Dashes, keine Verbots-Wörter, Sprache aus Config.

## Phase 5: Output schreiben

Datei: `{output_path}/[YYYY-MM-DD] Content Drafts.md`

Struktur:

```markdown
# Content Drafts [Datum]

## Quelle-Übersicht
- Gescannte Quellen: [N]
- Roh-Items: [N]
- Nach Filter: [N]
- Draft-Kandidaten: [N]
- Keyword-Quellen genutzt: [Ahrefs|SimilarWeb|Trends|Perplexity|Config]

## Trend-Snapshot
- Rising Keywords [Markt]: [Liste]
- Top Themen heute: [Liste]
- Branchen-Keywords (SimilarWeb, optional): [Liste]
- Hartes Volumen (Ahrefs, optional): [Top 5 Keywords mit Volume und Difficulty]

---

## Draft 1: [Working Title]

**Quelle:** [URL]
**Veröffentlicht:** [Datum]
**Themen-Match:** [Theme-Name]
**Signal-Score:** [X/4]
**Primary Keyword:** [Wort] (Source: [Ahrefs|SimilarWeb|Trends|Perplexity|Config], Volume: [N], Difficulty: [N])
**Secondary Keywords:** [Liste mit Source-Tags]

### LinkedIn Draft
[Volltext]

Hashtags: [Liste]

### X Draft
[Volltext, max 280]

---

## Draft 2: [...]
[...]

---

## Archiv (nicht gedraftet, evtl. später)
- [Item Titel] -> [URL] (Score X/4)
```

## Phase 6: Chat-Zusammenfassung

Kurz im Chat:
- Anzahl gefundener Signale
- Top 1 Draft als Vorschau (LinkedIn-Version)
- Link zur vollständigen Datei
- Hinweis auf Trend-Snapshot

Keine vollständige Wiederholung. Detail in der Datei.

## Phase 7: Logging

1. Wenn `05_System/memory/` existiert: kurzen Run-Log nach `05_System/memory/scout-runs.jsonl` schreiben:
   ```json
   {"timestamp":"...", "items_scanned":N, "drafts_produced":N, "top_keyword":"...", "keyword_sources_used":["ahrefs","trends"], "fallback_only":false}
   ```
2. Nicht-genutzte aber starke Items in `archive` der Output-Datei.

## Regeln

- Sprache aus Config
- Keine Em-Dashes
- Keine Emojis (außer Config erlaubt explizit)
- Substanz vor Form
- Wenn nichts Relevantes gefunden: ehrlich sagen, keine Lückenfüller
- Keine generischen "Top 10 Tools" Listen
- Keywords organisch einbauen, nie keyword-stuffing
- Bei jedem Fallback (kein Trend-Check möglich): im Output markieren

## Fehlerbilder

| Problem | Verhalten |
|---------|-----------|
| Keine `config.md` | Abbruch + Hinweis auf `config.example.md` |
| Quelle nicht erreichbar | Skip + Log, weiter mit Rest |
| Pytrends blockiert | Fallback Perplexity, im Output markieren |
| Keine Items über Schwelle | Datei trotzdem schreiben, "Nichts Substanzielles heute" |
| Brand-Voice-Datei fehlt | Default-Voice aus Config nutzen, im Output markieren |
| Ahrefs/SimilarWeb nicht auth | Stufe skip, Cascade läuft mit Rest |
| Alle Keyword-Quellen aus | Nur Config-Backup-Keywords nutzen, im Output prominent markieren |
