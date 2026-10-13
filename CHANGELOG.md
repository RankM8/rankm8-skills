# Changelog

## 2026-10-13 – seo 1.0.0

Das Repo wird zum Plugin-Marketplace `rankm8` mit dem Plugin `seo`. Alle Skills liegen unter
`plugins/seo/skills/` und sind weiter per `npx skills add RankM8/rankm8-skills` installierbar.

**Bestehende Skills (Namen unverändert, jetzt auf Deutsch)**
- `rankm8-project-setup`, `rankm8-site-audit`, `rankm8-keyword-research`, `rankm8-content-pipeline`,
  `rankm8-reporting` → übersetzt, Fachbegriffe für Einsteiger erklärt, Abschnitt „Passt zusammen mit“;
  Tool-Aufrufe, Bestätigungspunkte und Fehlercodes unverändert. `rankm8-reporting` schreibt für
  Endkunden in einfacher Sprache.

**Neue Skills**
- `seo` → Einstieg, leitet nach Ziel an den passenden Skill.
- `seo-setup` → Verbindung prüfen (Plugin bringt das RankM8-MCP mit), alte npx-Kopien aufräumen, Credits erklären.
- `seo-grundlagen` → SEO und GEO einfach erklärt, ehrliche Erwartungen, Glossar.
- `seo-schnellcheck` → fremde oder eigene Website ohne Projekt prüfen (`quick_check`), Report-Link
  (`share_quick_check`); Rückfallweg über `fetch_website`, `domain_keywords`, `serp_search`.
- `seo-lokal` → Unternehmensprofil, Kartenbereich, Bewertungen ohne Regelverstöße, Standortseiten.
- `seo-geo` → KI-Crawler richtig einordnen, Lesbarkeit, strukturierte Daten, Zitierbarkeit, fertige `llms.txt`.
- `seo-umsetzen` → Änderungen im Code oder Baukasten einbauen, Audit-Punkte erst nach Bestätigung abhaken.
- `seo-verkaufen` → Pakete, Preis-Rechenweg, Angebot, Erstkontakt mit Report-Link, keine Garantien.
