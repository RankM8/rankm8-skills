---
name: rankm8-keyword-research
description: Findet, prüft, importiert, analysiert, verknüpft und verwaltet SEO-Keywords über RankM8 MCP (Vorschläge, SERPs, Mitbewerber-Keywords, Seitenzuordnung, Rankings). Greift bei „Keywords finden“, „Keyword-Recherche“, „Keyword Research“, „welche Suchbegriffe passen zu meiner Seite“, „Mitbewerber-Keywords“ oder „Keywords einer Seite zuordnen“.
---

# RankM8 Keyword-Recherche

## Zweck

Recherchiere neue Keyword-Chancen, vergleiche SERPs und Mitbewerber-Domains, importiere
Keywords in RankM8, reichere sie mit Kennzahlen an, führe die Analyse aus und pflege die Beziehungen
zwischen Keywords und Seiten.

Erkläre Fachbegriffe beim ersten Vorkommen in einfachen Worten, der Nutzer ist oft Einsteiger.
Das gilt zum Beispiel für Keyword (Suchbegriff), SERP (Ergebnisseite einer Suche), Suchvolumen,
CPC (Preis pro Klick bei Werbeanzeigen), Suchintention und Keyword-Fit (wie gut ein Keyword zur Website passt).

## Benötigte MCP-Tools

- `keyword_research(keyword, type, project_id?, language?, location_code?, include_serp_info?, limit?)`
- `domain_keywords(target, project_id?, language?, location_code?, limit?)`
- `serp_search(query, project_id?, language?, location_code?)`

- `search_keywords(project_id, query?, limit?)`
- `get_keyword_details(keyword_id)`
- `list_keyword_serps(keyword_id, limit?, offset?)`
- `analyze_keywords(project_id, keyword_ids)`
- `add_keywords(project_id, keywords, sitemap_ids?)`
- `link_keyword_to_page(project_id, keyword_id, sitemap_id)`
- `unlink_keyword_from_page(project_id, keyword_id, sitemap_id)`
- `set_keyword_rankings(project_id, keyword_ids, sitemap_ids?, is_ranking)`
- `delete_keywords(project_id, keyword_ids, confirm)`
- `fetch_keyword_data(project_id)`
- `get_setup_status(project_id)`

- `list_projects(limit?, offset?)`
- `list_pages(project_id, search?, limit?, offset?)`

## Ablauf

1. Ermittle und bestätige die `project_id`; verwende bei Bedarf `list_projects()`.
2. Frage nach Seed-Keywords, Mitbewerber-Domains, Zielmarkt, Sprache und Standort.
3. Bestätige die Credit-Nutzung vor jedem Aufruf, der DataForSEO nutzt.
4. Rufe für die Erweiterung ausgehend von einem Seed `keyword_research(keyword, type="suggestions",
   project_id?, language?, location_code?, include_serp_info?, limit?)` auf.
5. Rufe für die semantische Erweiterung `keyword_research(keyword, type="related",
   project_id?, language?, location_code?, include_serp_info?, limit?)` auf.
6. Rufe für die Mitbewerber-Recherche `domain_keywords(target, project_id?,
   language?, location_code?, limit?)` auf.
7. Rufe für die Live-SERP-Prüfung `serp_search(query, project_id?, language?,
   location_code?)` auf. Übergib bevorzugt `project_id`, damit zwischengespeicherte Projekt-SERPs
   genutzt werden, wenn sie vorhanden sind.
8. Führe die Kandidaten zusammen, entferne Duplikate, vereinheitliche die Groß- und Kleinschreibung und gruppiere nach
   Suchintention, Seitenpassung, Suchvolumen, CPC und Ranking-Chance.
9. Zeige die Kandidatenliste und frage den Nutzer, welche Keywords importiert werden sollen.
10. Rufe `add_keywords(project_id, keywords=[...], sitemap_ids?)` auf. Ohne
    `sitemap_ids` gelten die Keywords projektweit; mit `sitemap_ids` werden sie
    mit den gewählten Seiten verknüpft.
11. Bestätige nach dem Hinzufügen der Keywords die Credit-Nutzung für die Anreicherung mit Kennzahlen.
12. Rufe `fetch_keyword_data(project_id)` auf.
13. Frage `get_setup_status(project_id)` alle 15-30 Sekunden ab, bis
    `steps.keyword_data.status` den Wert `completed` oder `completed_with_failures` hat.
14. Rufe `search_keywords(project_id, query?, limit=100)` auf, um IDs und
    angereicherte Kennzahlen zu erhalten.
15. Frage, welche Keywords die vollständige Wettbewerbsanalyse erhalten sollen.
16. Bestätige die Credit-Nutzung für `analyze_keywords`.
17. Rufe `analyze_keywords(project_id, keyword_ids=[...])` mit 1-20 IDs auf.
18. Frage `get_keyword_details(keyword_id)` alle 30 Sekunden für jedes analysierte
    Keyword ab, bis `keyword_fit` nicht mehr `null` ist oder der Nutzer beschließt abzubrechen.
19. Nutze `list_keyword_serps(keyword_id, limit?, offset?)`, um gespeicherte SERPs zu prüfen.
20. Nutze `list_pages(project_id, search?, limit?, offset?)`, wenn du Keywords
    mit Seiten verknüpfst.
21. Rufe für bestätigte Zuordnungen `link_keyword_to_page(project_id, keyword_id, sitemap_id)` auf.
22. Rufe `set_keyword_rankings(project_id, keyword_ids=[...],
    sitemap_ids=[...], is_ranking=true)` auf, um zu markieren, welche Seiten für welche Keywords ranken.
23. Nutze `unlink_keyword_from_page(project_id, keyword_id, sitemap_id)` nur, nachdem
    der Nutzer das Entfernen dieser Beziehung bestätigt hat.

Fasse die Empfehlung am Ende in einfachen Worten zusammen: welche Keywords sich lohnen und warum.

## Polling

`keyword_research`, `domain_keywords` und `serp_search` sind synchron, verbrauchen aber bei
Live-Aufrufen Credits. `fetch_keyword_data` ist asynchron und wird über
`get_setup_status(project_id)` abgefragt. `analyze_keywords` verhält sich ähnlich asynchron und wird abgefragt, indem du
alle 30 Sekunden `get_keyword_details(keyword_id)` aufrufst, bis `keyword_fit` gesetzt ist.

Frage nicht endlos ab. Wenn sich Keyword-Kennzahlen oder `keyword_fit` nach mehreren
Abfragen nicht ändern, fasse den stehengebliebenen Zustand zusammen und frage, ob der kostenpflichtige Schritt wiederholt werden soll.

## Bestätigung durch den Nutzer

Bestätige immer, bevor du:

- `keyword_research`, `domain_keywords` oder einen Live-`serp_search` aufrufst.
- `fetch_keyword_data` aufrufst.
- `analyze_keywords` aufrufst.
- importierte Keywords zu einem Projekt hinzufügst.
- Beziehungen verknüpfst, löschst oder Rankings setzt.
- `delete_keywords(project_id, keyword_ids, confirm=true)` aufrufst.

Rufe bei Löschungen zuerst `delete_keywords(project_id, keyword_ids=[...],
confirm=false)` auf oder lasse `confirm` weg, um die Vorschau zu zeigen. Rufe nur mit
`confirm=true` auf, nachdem der Nutzer ausdrücklich zugestimmt hat.

## Fehlerbehandlung

- `missing_keyword` oder `missing_query`: Bitte um einen nicht leeren Seed oder eine SERP-Abfrage.
- `missing_target`: Bitte um eine Domain oder eine vollständige URL.
- `insufficient_credits`: Stoppe vor dem kostenpflichtigen Aufruf und bitte den Nutzer, Credits aufzuladen.
- `project_not_found`: Wähle das Projekt neu aus oder stelle die OAuth-Verbindung neu her.
- `research_failed`, `lookup_failed` oder `search_failed`: Erkläre den Fehler des Anbieters und wiederhole
  den Aufruf nur nach Zustimmung des Nutzers.
- `missing_keywords` oder `too_many_keywords`: Übergib 1-100 Keywords an `add_keywords`.
- `missing_keyword_ids` oder `too_many_keywords`: Übergib 1-20 IDs an `analyze_keywords`.
- `invalid_keyword_ids` oder `invalid_sitemap_ids`: Lade Keywords bzw. Seiten neu und verwende
  IDs aus dem aktuellen Projekt.
- `missing_sitemap_ids`: Übergib Seiten-IDs, wenn `is_ranking=true` gilt.
- `not_linked`: Behandle das Entfernen der Verknüpfung als bereits erledigt und melde es.
- `confirmation_required`: Frage den Nutzer vor dem endgültigen Löschen.
- `dispatch_failed`: Stoppe und frage, bevor du die Aktion wiederholst.

## Passt zusammen mit

- Davor: `rankm8-project-setup` (Projekt und Seiten müssen existieren), `seo-grundlagen` für Begriffe wie Suchintention.
- Danach: `rankm8-content-pipeline`, um aus den Keywords Pläne, Gliederungen und Inhalte zu erstellen.
- Für lokale Suche: `seo-lokal`. Für Sichtbarkeit in KI-Antworten: `seo-geo`.
- Auswertung der Wirkung: `rankm8-reporting`.
