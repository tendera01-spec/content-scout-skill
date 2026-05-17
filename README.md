# Content Scout Skill

Tägliche Post-Drafts aus RSS-Feeds (z.B. Google Alerts), Branchenquellen und Trend-Signalen. Themen-agnostisch über `config.md`. Für Claude (Cowork, Claude Code, Obsidian-Vault-Setups).

## Schnellster Start: der Wizard

Du musst NICHTS manuell konfigurieren. Der Skill bringt einen Setup-Wizard mit, der dich in 6 bis 8 Fragen durch die Konfiguration führt.

So gehts:

1. Repo klonen oder Files in deinen Skill-Ordner kopieren
2. Im Chat schreiben: `content scout setup`
3. Fragen beantworten (Use Case, Sprache, Markt, Channels, etc.)
4. Wizard schreibt `config.md` und optional `brand-voice.md` automatisch
5. Fertig. Skill mit `content scout` starten

Der Wizard erkennt automatisch ob du Obsidian benutzt oder reines Claude Code, schlägt passende Pfade vor, und bietet 4 Themen-Presets zur Auswahl (AI Security, UX Design & Solo Founder, Marketing & Growth, Sales & B2B) plus Custom.

Wenn du beim ersten Mal `content scout` (ohne `setup`) schreibst und keine `config.md` existiert: Wizard startet automatisch.

## Manuelles Setup (falls bevorzugt)

Wenn du lieber YAML editierst statt Wizard zu benutzen:

1. `config.md` anlegen: Kopiere `config.example.md` oder eines aus `examples/` zu `config.md`
2. Drei Pfade ersetzen:
   - `output_path` Wohin Drafts geschrieben werden
   - `brand_voice_ref` Pfad zu einer Datei mit deinem Schreibstil
   - `sources` Google-Alert-Feed-URLs (Setup unten)
3. RSS-Feeds besorgen: Google Alerts auf "Feed" statt "E-Mail" umstellen, URLs eintragen

Dann im Chat: `content scout`.

## Was der Skill macht

1. Liest konfigurierte Quellen (RSS, Web) via Firecrawl oder web_fetch
2. Filtert Items gegen deine Themen-Cluster
3. Checkt SEO-relevante Keywords via Cascade (siehe unten)
4. Draftet fertige Posts pro Kanal (LinkedIn, X, Blog, Newsletter) im Brand-Voice deiner Wahl
5. Schreibt Output in eine tägliche Markdown-Datei

## Keyword-Cascade

Der Skill nutzt die beste verfügbare Datenquelle für Keyword-Recherche. Reihenfolge nach Datenqualität:

| Stufe | Tool | Was es liefert | Voraussetzung |
|-------|------|----------------|---------------|
| 1 | Ahrefs | Hartes Suchvolumen, Difficulty, Related Keywords | MCP Auth (paid) |
| 2 | SimilarWeb | Branchen-Traffic, Konkurrenz-Keywords, Trending Topics | MCP Auth (paid) |
| 3 | pytrends | Google Trends, Rising Queries, Trend-Slope | kostenlos (pip) |
| 4 | Perplexity | Qualitative Trend-Signale, kuratierte Quellen | MCP (free Tier reicht) |
| 5 | Config-Backup | Manuelle Keywords aus `config.md` | immer aktiv (Floor) |

Pro Config aktivierbar/deaktivierbar via `keyword_sources` Block. Skill prüft pro Run welche Stufen verfügbar sind und nutzt das beste verfügbare Setup. Im Output wird markiert welche Quelle jedes Keyword geliefert hat.

Output-Beispiel:

```
00_Daily_Notes/2026-05-17 Content Drafts.md
  Quelle-Übersicht
  Trend-Snapshot
  Draft 1 (LinkedIn + X)
  Draft 2 (LinkedIn + X)
  Archiv
```

## Installation

### Option A: Obsidian Vault mit Claude

1. Klone oder lade dieses Repo runter
2. Kopiere den gesamten Ordner nach `<dein-vault>/05_System/skills/content-scout/`
3. Benenne `config.example.md` zu `config.md` um (oder kopiere ein Beispiel aus `examples/`)
4. Trage deine Themen, Quellen und Brand-Voice-Pfad ein
5. Im Chat: `content scout`

### Option B: Claude Code (ohne Obsidian)

1. Klone in `~/.claude/skills/content-scout/` (oder dein Skills-Verzeichnis)
2. `config.md` anlegen (siehe `config.example.md`):
   - `output_path: ./content-drafts` (relativ zum Projekt-Root)
   - `brand_voice_ref: ./brand-voice.md` (lege diese Datei im Projekt an)
   - Master-Rules-Block im SKILL.md Phase 0 wird automatisch übersprungen wenn keine vorhanden
3. Output-Ordner anlegen: `mkdir content-drafts`
4. Skill wird automatisch erkannt, wenn der Skill-Pfad geladen ist

### Minimum brand-voice.md

Wenn du keine Brand-Voice-Datei hast, leg eine an. Beispiel-Inhalt:

```markdown
# Brand Voice

## Tone
Direkt, klar, kein Filler. Kurze Sätze. Confident ohne Hedging.

## Verboten
- "innovativ", "cutting-edge", "game-changing"
- Hedging ("vielleicht", "möglicherweise")
- Marketing-Buzzwords ohne Substanz

## Erwünscht
- Konkrete Beispiele und Zahlen
- Eigene Meinung statt Zusammenfassung
- Kontrast durch Doppelpunkt oder kurzen Satz statt Gedankenstrich
```

Reicht für den Anfang, kann später erweitert werden.

## Google Alerts auf RSS umstellen

1. Gehe zu https://www.google.com/alerts
2. Bearbeite den Alert (Stift-Icon)
3. "Optionen anzeigen" → "An: Feed" wählen statt E-Mail
4. Speichern → RSS-Symbol erscheint
5. Rechtsklick auf RSS → Link kopieren
6. In `config.md` unter `sources` eintragen

## Konfiguration

Siehe `config.example.md` für alle Optionen. Mindest-Konfiguration:

- `themes` mit Primary- und Backup-Keywords
- `sources` mit mindestens einem RSS-Feed
- `output_path` (Pfad relativ zum Vault/Root)
- `brand_voice_ref` (Datei mit Tone-Regeln)
- `channels` (linkedin, x, blog, newsletter)

## Fertige Konfigurationen

Schau in `examples/`:

- `config.ai-security.md` AI Security, AI Agent Governance, EU AI Act, LLM Safety
- `config.solo-founder.md` Solo Founder mit AI, Creative Ops, UX Strategie

## Voraussetzungen

Minimum:
- Claude mit MCP-Tools für Firecrawl oder web_fetch (für RSS-Parsing)

Optional (für bessere Keyword-Daten, in dieser Reihenfolge wertvoll):
- Ahrefs MCP authentifiziert (Keyword Volume, Difficulty)
- SimilarWeb MCP authentifiziert (Branchen-Traffic, Konkurrenz-Keywords)
- Python mit `pytrends` (wird vom Skill bei Bedarf installiert)
- Perplexity MCP (qualitative Trends)

Skill läuft auch ohne die Optionalen, nutzt dann Config-Backup-Keywords als Floor.

## Lizenz

MIT. Frei nutzbar, forken erwünscht.

## Hinweise

- Skill schreibt keine Posts automatisch. Du reviewst die Drafts und postest manuell
- Bei jedem Run wird eine neue Markdown-Datei erstellt, alte werden nicht überschrieben
- Wenn nichts Relevantes gefunden wird: Datei meldet das ehrlich, keine Lückenfüller
- Keine Em-Dashes, keine Emojis (außer Config erlaubt es), Sprache aus Config
