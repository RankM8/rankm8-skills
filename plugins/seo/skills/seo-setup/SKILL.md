---
name: seo-setup
description: Richtet das Plugin seo ein und prüft die RankM8-Verbindung. Verwenden bei „SEO einrichten“, „RankM8 verbinden“, „RankM8 MCP“, „funktioniert RankM8“, „RankM8-Tools fehlen“ oder direkt nach der Installation des Plugins. Prüft die Verbindung, räumt alte Skill-Kopien auf und erklärt Credits, ohne kostenpflichtige Schritte.
---

# SEO einrichten

Ziel: Die RankM8-Verbindung steht, alte Skill-Kopien sind weg, der Nutzer weiß, was Credits kosten.
In diesem Ablauf nur lesende, kostenlose Aufrufe.

## 1. Umgebung erkennen

Claude Code (Shell und Dateisystem), Claude App / claude.ai / Cowork (keine Shell), ChatGPT oder
Codex, Cursor oder ein anderes KI-Werkzeug. In einem Satz nennen.

## 2. RankM8-Verbindung prüfen

`list_projects()` aufrufen (kostenlos).

- **Antwort kommt:** verbunden. Anzahl der Projekte nennen.
- **Tool fehlt:** Verbindung herstellen. Ein RankM8-Konto ist nötig (rankm8.com).
  - **Claude Code mit diesem Plugin:** Das Plugin bringt die Verbindung mit. `/mcp` öffnen, `rankm8`
    wählen und anmelden. Fehlt der Eintrag: `/reload-plugins` und neu starten.
  - **Claude Code ohne Plugin:** `claude mcp add --transport http rankm8 https://app.rankm8.com/_mcp/v1`,
    dann `/mcp` und anmelden.
  - **claude.ai / Claude App:** Einstellungen → Connectors → eigenen Connector hinzufügen → Adresse
    `https://app.rankm8.com/_mcp/v1` → anmelden.
  - **Andere Werkzeuge:** Anleitung in der RankM8-App unter `/mcp`.
- **Fehler `project_not_found` oder Anmeldefehler:** Verbindung trennen und neu anmelden.

Hinweis zu Berechtigungen: Bei der Anmeldung fragt RankM8 nach Bereichen (Projekte, Seiten,
Suchbegriffe, Integrationen, Werkzeuge, Inhalte). Für alle Skills alle Bereiche freigeben.

## 3. Was nur in der RankM8-App geht

Google Search Console, Google-Unternehmensprofil und WordPress werden in der Web-App unter den
Projekt-Einstellungen verbunden (Google-Anmeldung im Browser). Ohne diese Verbindungen fehlen
Klickzahlen und lokale Daten in Berichten.

## 4. Credits erklären

Lesen und Verwalten ist kostenlos. Credits kosten: Website-Analyse, Seiten finden und lesen,
Suchbegriff-Daten und -Analyse, Site-Audit, Schnellcheck, Live-Suchergebnisse und alle Texte und
Bilder. Die Skills nennen vor jedem kostenpflichtigen Schritt die Kosten und fragen nach.

Das aktuelle Guthaben liefert kein RankM8-Tool; es steht in der RankM8-App (app.rankm8.com). Keine
Zahl schätzen.

## 5. Alte Skill-Kopien aufräumen (nur Claude Code, Codex, Cursor)

Wer die Skills früher mit `npx skills add RankM8/rankm8-skills` installiert hat, hat Kopien z. B. in
`~/.claude/skills/`, `~/.agents/skills/`, `~/.codex/skills/` oder im Projekt unter `.claude/skills/`.
Mit dem Plugin wären sie doppelt da.

1. Ordner `rankm8-*` und `seo*` in diesen Verzeichnissen suchen.
2. Liste zeigen und fragen, ob sie gelöscht werden sollen.
3. Erst nach Bestätigung löschen, danach in Claude Code `/reload-plugins`.

Wer bewusst ohne Plugin arbeitet (z. B. in Cursor), behält die Kopien und aktualisiert sie mit
`npx skills add RankM8/rankm8-skills --all`.

## 6. Updates

In Claude Code sind Updates fremder Marketplaces standardmäßig aus. Empfehlen: `/plugin` →
Marketplaces → `rankm8` → Auto-Update einschalten. Sonst manuell: `/plugin marketplace update rankm8`.

## Abschluss

Kurz berichten: Umgebung, Verbindung ja/nein, Anzahl Projekte, aufgeräumte Kopien, Auto-Update.
Danach weiter mit `seo`.
