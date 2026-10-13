---
name: rankm8-reporting
description: Erstellt einen monatlichen SEO-Bericht aus RankM8-Projekten, Search Console, Google-Unternehmensprofil und getrackten Keywords. Greift bei „SEO-Bericht“, „Monatsbericht“, „Report erstellen“, „wie läuft meine Website“, „Auswertung für den Kunden“ oder „Reporting“.
---

# RankM8 Monatsbericht

## Zweck

Erstelle einen kompakten monatlichen SEO-Bericht, der die Leistung aus der Search Console,
Einblicke aus dem Google-Unternehmensprofil für lokale Projekte und die getrackten Keyword-Daten
von RankM8 kombiniert. Konzentriere dich auf geschäftliche Ergebnisse, Veränderungen gegenüber dem Vorzeitraum,
Gewinner, Verlierer und empfohlene nächste Schritte.

Erkläre Fachbegriffe beim ersten Vorkommen in einfachen Worten, der Nutzer ist oft Einsteiger.
Das gilt zum Beispiel für Impressionen (wie oft eine Seite in der Suche angezeigt wurde), Klicks,
durchschnittliche Position und Search Console (kostenloses Google-Werkzeug mit Suchdaten).

## Benötigte MCP-Tools

Reporting-Tools:

- `get_gsc_performance(project_id, dimension?, url?, query?, start_date?, end_date?, date_range?, compare_with_previous?, limit?, sort_by?)`
- `get_gmb_data(project_id, date_range?, include_keywords?, keyword_limit?)`
- `search_keywords(project_id, query?, limit?)`

Hilfreiche unterstützende Tools:

- `list_projects(limit?, offset?)`
- `get_project(project_id)`
- `list_pages(project_id, search?, limit?, offset?)`
- `list_project_audit_issues(project_id)`

Optionale Tools zur Vertiefung:

- `get_keyword_details(keyword_id)`
- `list_keyword_serps(keyword_id, limit?, offset?)`
- `get_gsc_performance(project_id, dimension="query", query?, start_date?, end_date?, date_range?, compare_with_previous?, limit?, sort_by?)`
- `get_gsc_performance(project_id, dimension="page", url?, start_date?, end_date?, date_range?, compare_with_previous?, limit?, sort_by?)`
- `get_gsc_performance(project_id, dimension="date", start_date?, end_date?, date_range?, compare_with_previous?, limit?, sort_by?)`

## Ablauf

1. Ermittle und bestätige das Berichtsprojekt. Verwende `list_projects()`, wenn die
   `project_id` unbekannt ist.
2. Frage nach dem Berichtsmonat, falls er nicht eindeutig ist. Verwende für einen Monatsbericht bevorzugt exakte
   `start_date` und `end_date` im Format `YYYY-MM-DD`.
3. Rufe `get_project(project_id)` auf, um Projekt-URL, Scope und Status zu verstehen.
4. Rufe `get_gsc_performance(project_id, start_date, end_date,
   compare_with_previous=true, limit=25, sort_by="clicks")` für die
   Zusammenfassung auf.
5. Rufe, wenn GSC verbunden ist, `get_gsc_performance(project_id, dimension="query",
   start_date, end_date, compare_with_previous=true, limit=25,
   sort_by="clicks")` für die Top-Suchanfragen auf.
6. Rufe `get_gsc_performance(project_id, dimension="query", start_date,
   end_date, limit=25, sort_by="impressions")` auf, um Chancen mit vielen Impressionen zu finden.
7. Rufe `get_gsc_performance(project_id, dimension="page", start_date,
   end_date, compare_with_previous=true, limit=25, sort_by="clicks")` für die
   stärksten Landingpages auf.
8. Rufe `get_gsc_performance(project_id, dimension="date", start_date,
   end_date, limit=100, sort_by="clicks")` für den Trendverlauf auf.
9. Rufe `search_keywords(project_id, query?, limit=100)` auf, um Suchvolumen, Fit, Suchintention
   und Ranking-Status der getrackten Keywords zusammenzufassen.
10. Rufe für lokale Projekte `get_gmb_data(project_id, date_range="28d",
    include_keywords=true, keyword_limit=20)` auf. Ist der Monat länger oder kürzer,
    weise darauf hin, dass die GMB-Insight-Zeilen auf der verfügbaren 30-Tage-Zusammenfassung in der Datenbank beruhen.
11. Rufe optional `list_project_audit_issues(project_id)` auf, um eine Momentaufnahme der technischen SEO-Risiken
    zu ergänzen.
12. Baue den Bericht mit den Abschnitten: Executive Summary, GSC Performance, Query
    Winners and Opportunities, Landing Pages, Keyword Portfolio, Local Performance,
    Technical Risks, Recommended Actions und Data Caveats.
13. Nimm Veränderungen gegenüber dem Vorzeitraum auf, wenn `comparison.changes` verfügbar ist.
    Beachte: Eine negative Veränderung bei der durchschnittlichen Position bedeutet eine Verbesserung.
14. Wenn `connected=false` für GSC oder GMB gilt, nimm einen Hinweis zu den Daten (Data Caveats) auf, statt fehlende Daten
    als null Leistung zu werten.
15. Priorisiere Empfehlungen nach geschätzter Wirkung und Aufwand und verknüpfe jede
    Empfehlung mit der beobachteten Kennzahl.
16. Für Endkunden in einfacher Sprache schreiben, ohne Fachjargon.

## Polling

Die Reporting-Tools dieses Playbooks sind synchron. Frage
`get_gsc_performance`, `get_gmb_data` oder `search_keywords` nicht per Polling ab.

Wenn der Nutzer im Rahmen des Berichts eine frische Keyword-Analyse wünscht, wechsle zum
Keyword-Recherche-Playbook: Bestätige die Credit-Nutzung, rufe
`analyze_keywords(project_id, keyword_ids)` auf und frage `get_keyword_details(keyword_id)`
alle 30 Sekunden ab, bis `keyword_fit` verfügbar ist. Verstecke diesen kostenpflichtigen Abstecher nicht
in einem normalen Monatsbericht.

## Bestätigung durch den Nutzer

Bestätige immer, bevor du:

- das Projekt auswählst, wenn mehrere Projekte passen.
- einen benutzerdefinierten Zeitraum verwendest, wenn die Formulierung des Nutzers mehrdeutig ist.
- optionale kostenpflichtige Analysen außerhalb der Reporting-Tools ausführst.
- den Bericht außerhalb des aktuellen KI-Clients teilst oder exportierst.
- Issues oder Aufgaben in einem externen System als erledigt markierst.

In diesem Reporting-Workflow gibt es keine Lösch-Tools. Wenn der Nutzer beim Durchsehen des Berichts
das Löschen von Keywords oder Seiten verlangt, wechsle zum passenden Wartungs-Playbook und verlange eine
ausdrückliche Bestätigung für den destruktiven Schritt.

## Fehlerbehandlung

- `project_not_found`: Starte die Projektauswahl erneut oder bitte den Nutzer, die OAuth-Verbindung neu herzustellen.
- `invalid_dimension`: Verwende nur `query`, `page` oder `date`.
- `connected=false` von `get_gsc_performance`: Melde, dass die Google Search Console
  nicht verbunden ist, und lasse GSC-Aussagen weg.
- `connected=false` mit `notRelevant=true` von `get_gmb_data`: Melde, dass die Berichterstattung zum Google-Unternehmensprofil
  nur für lokale Projekte relevant ist.
- `connected=false` von `get_gmb_data`: Melde, dass das Google-Unternehmensprofil nicht
  verbunden ist, und lasse lokale Einblicke weg.
- Leere Keyword-Ergebnisse von `search_keywords`: Melde, dass für das Projekt keine getrackten Keywords
  verfügbar waren.
- Ungültiger `date_range`: Verwende `7d`, `28d` oder `90d` oder übergib exakte `start_date` und
  `end_date` für Monatsberichte.

## Passt zusammen mit

- Davor: `rankm8-project-setup` (Projekt und Verbindungen), `seo-setup` falls Search Console oder Verbindung fehlen.
- Zusätzliche Daten: `rankm8-site-audit` für technische Risiken, `seo-lokal` für das Google-Unternehmensprofil.
- Danach: `seo-umsetzen` für die empfohlenen Maßnahmen, `seo-verkaufen`, wenn SEO als Dienstleistung angeboten wird.
- Begriffe für Einsteiger: `seo-grundlagen`.
