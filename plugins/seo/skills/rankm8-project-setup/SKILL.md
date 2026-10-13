---
name: rankm8-project-setup
description: Legt ein neues RankM8-SEO-Projekt an und führt die komplette Einrichtung über den RankM8-MCP-Server durch (Seiten finden, crawlen, Keywords anreichern und analysieren, Ziele und Mitbewerber speichern). Greift bei „Projekt anlegen“, „Website in RankM8 einrichten“, „neues SEO-Projekt starten“, „Projekt-Setup“ oder „Domain in RankM8 aufnehmen“.
---

# RankM8 Projekt-Setup

## Zweck

Führe den Nutzer durch die komplette Einrichtung eines RankM8-Projekts, ohne die Web-Oberfläche zu öffnen.
Das Playbook legt das Projekt an, entdeckt Seiten, crawlt ausgewählte Seiten, reichert
Keywords mit Daten an, führt die Keyword-Analyse aus und speichert Ziele sowie Mitbewerber.

Der MCP-Server ist die maßgebliche Quelle. Merke dir die `project_id`, die `create_project`
zurückgibt, und übergib sie an jeden folgenden projektbezogenen Aufruf.

Erkläre Fachbegriffe beim ersten Vorkommen in einfachen Worten, der Nutzer ist oft Einsteiger.
Das gilt zum Beispiel für Sitemap (Verzeichnis der Seiten einer Website), Crawling (automatisches Auslesen der Seiten),
Keyword (Suchbegriff), Mitbewerber (Websites, die für dieselben Suchbegriffe sichtbar sind) und Credits (Guthaben für kostenpflichtige Schritte).

## Benötigte MCP-Tools

Setup-Tools:

- `analyze_website(url)`
- `create_project(name, project_url, company_name, company_location, company_country, language, project_scope, coordinates?)`
- `scrape_sitemap(project_id, sitemap_url?)`
- `serp_site_search(project_id)`
- `crawl_pages(project_id, sitemap_ids)`
- `fetch_keyword_data(project_id)`
- `suggest_selection(project_id, type, limit?, sitemap_ids?)`
- `list_competitor_candidates(project_id, keyword_ids?, max_position?, limit?)`
- `get_setup_status(project_id)`
- `update_project(project_id, name?, company_name?, company_location?, company_country?, language?, project_scope?, coordinates?, company_description?, business_goals?, selected_competitors?, selected_sitemap_ids?, selected_keyword_ids?)`

Tool für die Übergabe an die Pipeline:

- `analyze_keywords(project_id, keyword_ids)`

## Ablauf

1. Frage nach der Ziel-Website-URL, falls sie fehlt.
2. Bestätige die Credit-Nutzung, dann rufe `analyze_website(url)` auf, um `company_name`,
   `company_location`, `company_country` und `language` abzuleiten.
3. Zeige dem Nutzer die abgeleiteten Projektdaten und bitte ihn, sie vor dem Anlegen
   des Projekts zu bestätigen oder zu korrigieren.
4. Rufe `create_project(name, project_url, company_name, company_location,
   company_country, language, project_scope, coordinates?)` auf.
5. Speichere die zurückgegebene `project_id` für jeden weiteren Aufruf.
6. Bestätige die Credit-Nutzung für die Seitenermittlung, dann rufe
   `scrape_sitemap(project_id, sitemap_url?)` auf.
7. Frage `get_setup_status(project_id)` alle 15-30 Sekunden ab, bis
   `steps.sitemap_scrape.status` den Wert `completed` oder `completed_with_failures` hat.
8. Wenn `steps.sitemap_scrape.status` den Wert `failed` hat, wenn `pages_found` gleich `0` ist
   oder wenn die Antwort einen Fallback vorschlägt, frage den Nutzer vor dem kostenpflichtigen Fallback
   und rufe dann `serp_site_search(project_id)` auf.
9. Rufe `suggest_selection(project_id, type="pages", limit=10)` auf.
10. Zeige die Seitenvorschläge, URLs und Begründungen und bitte den Nutzer, die endgültige
    Liste der `sitemap_ids` zu bestätigen oder zu ändern, idealerweise 5-10 Seiten und nie mehr als 25.
11. Bestätige die Credit-Nutzung, dann rufe `crawl_pages(project_id, sitemap_ids=[...])` auf.
12. Frage `get_setup_status(project_id)` alle 15-30 Sekunden ab, bis
    `steps.keyword_extraction.status` den Wert `completed` oder `completed_with_failures` hat.
13. Bestätige die Credit-Nutzung, dann rufe `fetch_keyword_data(project_id)` auf.
14. Frage `get_setup_status(project_id)` alle 15-30 Sekunden ab, bis
    `steps.keyword_data.status` den Wert `completed` oder `completed_with_failures` hat.
15. Rufe `suggest_selection(project_id, type="keywords", limit=10,
    sitemap_ids=[...])` auf, wenn seitenbezogene Vorschläge hilfreich sind.
16. Zeige die Keyword-Vorschläge, Kennzahlen und Begründungen und bitte den Nutzer, bis zu
    10 `keyword_ids` zu bestätigen.
17. Rufe `update_project(project_id, selected_keyword_ids=[...])` vor der Keyword-Analyse auf,
    damit der Setup-Status auf die bestätigten Keywords begrenzt ist.
18. Bestätige die Credit-Nutzung, dann rufe `analyze_keywords(project_id, keyword_ids=[...])` auf.
19. Frage `get_setup_status(project_id)` alle 15-30 Sekunden ab, bis
    `steps.keyword_analysis.status` den Wert `completed` oder `completed_with_failures` hat.
20. Rufe `list_competitor_candidates(project_id, keyword_ids=[...],
    max_position=10, limit=10)` auf.
21. Zeige die Kandidaten und bitte den Nutzer, bis zu 3-5 Mitbewerber-Domains auszuwählen.
22. Frage nach bis zu 3 Geschäftszielen aus `more_traffic`, `more_leads`,
    `higher_sales`, `brand_awareness`, `local_visibility`.
23. Rufe `update_project(project_id, business_goals=[...],
    selected_competitors=[...], selected_sitemap_ids=[...],
    selected_keyword_ids=[...])` auf.

## Polling

Behandle `get_setup_status(project_id)` als das einzige Polling-Tool für das Setup. Beobachte
`overall_status`, `current_step`, `steps`, `poll_again_in_seconds`, `next_action`
und `warning`.

Gültige Step-Status sind `not_started`, `pending`, `running`, `completed`,
`completed_with_failures` und `failed`.

Pausiere beim Warten freundlich und sage dem Nutzer, dass der Schritt einige Minuten dauern kann.
Frage niemals endlos ab. Wenn eine Antwort `warning` enthält, erkläre sie dem Nutzer und
wiederhole entweder das angegebene Step-Tool, wechsle zu einem dokumentierten Fallback oder brich ab.
Wenn ein Step `failed` ist, stoppe die Pipeline und behandle den Fehler, bevor du weitermachst.

## Bestätigung durch den Nutzer

Hole immer eine ausdrückliche Bestätigung des Nutzers ein, bevor du:

- ein Credit-verbrauchendes Tool aufrufst: `analyze_website`, `scrape_sitemap`,
  `serp_site_search`, `crawl_pages`, `fetch_keyword_data`, `analyze_keywords`.
- `create_project` mit den endgültigen Projektdaten aufrufst.
- die ausgewählten `sitemap_ids` crawlst.
- die ausgewählten `keyword_ids` analysierst.
- `selected_competitors` und `business_goals` speicherst.

Die Verbindungen zu GSC (Google Search Console), GMB (Google-Unternehmensprofil) und WordPress lassen sich nicht über MCP herstellen.
Verweise den Nutzer für diese OAuth-Abläufe im Browser auf die Projekteinstellungen in der RankM8-Web-Oberfläche.

## Fehlerbehandlung

- `project_limit_reached`: Sage dem Nutzer, dass das Limit seines Tarifs erreicht ist, und schlage
  ein Upgrade oder das Löschen ungenutzter Projekte vor.
- `insufficient_credits`: Stoppe vor dem kostenpflichtigen Schritt und bitte den Nutzer, Credits aufzuladen.
- `invalid_parameter`: Zeige das ungültige Feld und bitte um korrigierte Eingaben.
- `invalid_url` oder `missing_url`: Bitte um eine öffentliche HTTP(S)-Website-URL.
- `invalid_sitemap_ids` oder `invalid_keyword_ids`: Liste Seiten bzw. Keywords erneut auf und bitte
  den Nutzer, IDs aus dem aktuellen Projekt zu wählen.
- `missing_sitemap_ids` oder `too_many_pages`: Bitte um 1-25 Seiten-IDs.
- `analysis_failed`, `search_failed` oder `dispatch_failed`: Stoppe, fasse den fehlgeschlagenen Schritt
  zusammen und wiederhole ihn erst, nachdem der Nutzer zugestimmt hat.
- `project_not_found`: Starte die Projektauswahl erneut oder bitte den Nutzer, die OAuth-Verbindung neu herzustellen.

## Passt zusammen mit

- Davor: `seo-setup`, falls die Verbindung zu RankM8 noch nicht eingerichtet ist. `seo-grundlagen`, wenn dem Nutzer Begriffe unklar sind.
- Danach: `rankm8-site-audit` für den technischen Check einzelner Seiten, `rankm8-keyword-research` für weitere Keywords.
- Für lokale Unternehmen: `seo-lokal` (Google-Unternehmensprofil, lokale Suche).
- Einstieg und Weiterleitung: `seo`.
