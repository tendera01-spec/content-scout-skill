# Content Scout Setup Wizard

Wird automatisch gestartet wenn der User `content scout` triggert und keine `config.md` existiert. Kann auch jederzeit manuell aufgerufen werden mit `content scout setup`.

## Ziel

Den User in 6 bis 8 Fragen durch die Konfiguration führen, ohne dass er YAML manuell editieren muss. Am Ende schreibt der Wizard `config.md` und optional `brand-voice.md`.

## Prinzip

- Eine Frage nach der anderen, nie alle auf einmal
- Smart Defaults: aktuelles Verzeichnis erkennen, Vault-Setup erkennen, vernünftige Vorbelegung
- Presets statt freier Texteingabe wo möglich
- Bestätigung am Ende, vor dem Schreiben
- Wenn User unsicher ist: "weiß nicht" oder "skip" akzeptieren und vernünftigen Default setzen

## Ablauf

### Schritt 0: Begrüßung und Umgebungs-Check

Bevor Fragen gestellt werden, prüfen:

1. Existiert `00_Daily_Notes/` oder `05_System/` im aktuellen Verzeichnis? → Obsidian-Vault erkannt
2. Existiert `.git/` oder ein anderer Projekt-Marker? → Code-Projekt erkannt
3. Sonst: einfacher Ordner, Defaults setzen

Begrüßungs-Text (anpassen je nach Setup):

> Hi. Ich richte den Content Scout für dich ein. Ich frage dich 6 bis 8 Sachen, danach kannst du loslegen. Wenn du etwas nicht weißt, sag einfach "weiß nicht", ich setze dann einen vernünftigen Default. Bereit?

### Schritt 1: Use Case (Themen-Cluster)

Frage:
> Welche Themen sollen gescoutet werden? Wähle ein Preset oder beschreibe deinen Use Case.

Optionen (per AskUserQuestion oder Multiple Choice):
- **A) AI Security & Governance** AI Security, LLM Safety, AI Agent Governance, EU AI Act
- **B) UX Design & Solo Founder** AI in Design, Creative Ops, UX Strategie, Solopreneurship
- **C) Marketing & Growth** SEO, Content Marketing, Performance, Growth Hacking
- **D) Sales & B2B** Outbound, Pipeline, Enterprise Sales, RevOps
- **E) Custom** Themen frei eingeben

Bei A bis D: Themen aus `presets/[name].md` laden. Bei E: in 2 bis 4 Cluster aufteilen, je 3 bis 5 Keywords pro Cluster.

### Schritt 2: Sprache

Frage:
> In welcher Sprache sollen die Drafts geschrieben werden?

Optionen:
- Deutsch
- Englisch
- Beide (je nach Quelle)

### Schritt 3: Zielmarkt (für Google Trends)

Frage:
> Welcher Markt? Wichtig für Trend-Daten.

Optionen:
- DE (Deutschland)
- CH (Schweiz)
- AT (Österreich)
- US
- DACH (DE + CH + AT)
- EU
- Global

### Schritt 4: Kanäle

Frage:
> Wo willst du posten?

Multiple-Select:
- LinkedIn
- X (Twitter)
- Blog
- Newsletter

Default: LinkedIn + X

### Schritt 5: Output-Pfad

Frage (mit Smart Default):

Wenn Obsidian-Vault erkannt:
> Soll der Output in `00_Daily_Notes/` landen? (Standard für Obsidian)
> Optionen: Ja / Anderen Pfad eingeben

Wenn Code-Projekt oder einfacher Ordner:
> Wo sollen die Drafts hin? Standard: `./content-drafts` im aktuellen Ordner.
> Optionen: Default verwenden / Anderen Pfad eingeben

Bei "Anderen Pfad": Text-Eingabe akzeptieren.

### Schritt 6: Brand-Voice

Erst prüfen ob es bereits eine `brand-voice.md`, `brand.md`, oder ähnliche Datei im Projekt gibt.

Wenn ja:
> Ich habe `[pfad]` gefunden. Als Brand-Voice nutzen?
> Optionen: Ja / Andere Datei wählen / Neue anlegen

Wenn nein:
> Ich brauche eine Datei mit deinem Schreibstil. Drei Optionen:
> A) Du hast schon eine, gib mir den Pfad
> B) Ich erstelle eine Minimal-Vorlage, du editierst sie später
> C) Mini-Wizard: Ich frage dich 3 Sachen und schreibe sie dir

Wenn C: nächste Sektion.

### Schritt 6b: Mini-Brand-Voice-Wizard (optional)

Nur wenn User in Schritt 6 Option C gewählt hat:

Frage 1:
> Beschreibe deinen Schreibstil in einem Satz. Beispiele: "Direkt, knapp, kein Filler" oder "Warm, persönlich, mit Anekdoten" oder "Analytisch, datengetrieben, mit Zahlen".

Frage 2:
> Welche Wörter oder Phrasen willst du nie nutzen? Beispiele: "innovativ", "cutting-edge", "leverage", Marketing-Buzzwords.

Frage 3:
> Gib mir ein bis zwei kurze Beispiele wie du normalerweise schreibst. Kann ein Tweet sein, ein LinkedIn-Hook, oder ein Satz aus einem Blog-Post.

Aus den Antworten `brand-voice.md` schreiben (siehe Template unten).

### Schritt 7: Google Alerts / RSS

Frage:
> Hast du schon Google Alerts mit RSS-Feeds?

Optionen:
- **Ja, gib mir die URLs** Text-Eingabe, eine URL pro Zeile
- **Nein, zeig mir wie** Anleitung anzeigen (siehe unten), URLs später eintragen
- **Skip** Config wird mit Platzhaltern erstellt, User trägt später ein

Wenn "Nein, zeig mir wie": Anleitung:

> So machst du das in 60 Sekunden:
> 1. Gehe zu https://www.google.com/alerts
> 2. Suchbegriff eingeben (z.B. "AI Security")
> 3. Auf "Optionen anzeigen" klicken
> 4. Bei "Senden an" auf "Feed" wechseln statt E-Mail
> 5. "Alert erstellen" klicken
> 6. Auf der Übersichtsseite: RSS-Symbol neben dem Alert klicken, URL kopieren
> 7. Sag mir die URL oder trage sie später in `config.md` ein

### Schritt 8: Keyword-Quellen (optional)

Frage:
> Welche Keyword-Tools willst du nutzen? Je mehr authentifiziert ist, desto bessere SEO-Daten.

Multiple-Select mit Status-Anzeige:
- Ahrefs (paid) [Status: authenticated / not connected]
- SimilarWeb (paid) [Status: authenticated / not connected]
- Google Trends [immer aktiv]
- Perplexity [Status: authenticated / not connected]

Default: alle aktiviert die verfügbar sind. Nicht-verfügbare werden in Config auf `false` gesetzt.

### Schritt 9: Bestätigung

Komplette Config als Preview im Chat zeigen:

```yaml
# Content Scout Config (generiert vom Wizard)

language: de
frequency: daily
drafts_per_run: 2
min_signal_score: 3
output_path: ./content-drafts
brand_voice_ref: ./brand-voice.md
geo: DE,CH

themes:
  - name: "AI Security"
    primary_keywords: [...]
    backup_keywords: [...]

sources:
  - type: rss
    url: "https://www.google.com/alerts/feeds/..."
    name: "Google Alert: AI Security"

channels:
  - linkedin
  - x

keyword_sources:
  ahrefs: false
  similarweb: false
  pytrends: true
  perplexity: true
```

Frage:
> Sieht das gut aus? Optionen: Speichern / Einen Schritt zurück / Abbrechen

### Schritt 10: Schreiben

Bei Bestätigung:
1. `config.md` schreiben
2. `brand-voice.md` schreiben (falls in Schritt 6 erstellt)
3. Output-Ordner anlegen (`mkdir -p` auf `output_path`)
4. Log-Ordner anlegen falls separates `scout_log_path` konfiguriert
5. Bestätigungs-Message:

> Setup fertig. Files:
> - `config.md` erstellt
> - `brand-voice.md` erstellt (Schritt 6)
> - Output-Ordner `./content-drafts` angelegt
>
> Jetzt:
> - Wenn du keine RSS-URLs eingetragen hast: `config.md` öffnen, unter `sources` die URLs eintragen
> - Wenn Ahrefs/SimilarWeb/Perplexity nicht authentifiziert: später im Tool-Setup machen
> - Skill starten: "content scout"

## Brand-Voice Template (für Schritt 6b)

```markdown
# Brand Voice

## Tone
[Antwort aus Frage 1]

## Verboten
[Liste aus Frage 2]

## Erwünscht
- Substanz vor Form
- Eigene Meinung statt Zusammenfassung
- Konkrete Beispiele und Zahlen

## Beispiele
[Antworten aus Frage 3]
```

## Wizard-Regeln

- Eine Frage pro Antwort, nie mehrere stacken
- AskUserQuestion in Cowork nutzen, Plain Chat in Claude Code
- Bei Unsicherheit ("weiß nicht"): vernünftigen Default setzen, im Confirm-Schritt klar markieren
- User kann jederzeit "zurück" sagen, dann eine Frage rückwärts
- User kann "abbrechen" sagen, dann nichts schreiben
- Nach erfolgreichem Setup: in Output-Datei vermerken dass via Wizard erstellt
- Bei Re-Setup (config.md existiert): "Config existiert bereits. Überschreiben (J), Backup machen und neu (B), Abbrechen (A)?"
