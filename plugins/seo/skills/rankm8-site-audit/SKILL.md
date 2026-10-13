---
name: rankm8-site-audit
description: Führt technische SEO-Audits für einzelne RankM8-Projektseiten über MCP aus, wertet sie aus, sortiert die Probleme und pflegt den Status von Issues und Checks. Greift bei „Seite prüfen“, „technische SEO-Probleme finden“, „Website-Audit“, „Site Audit“, „Seitenanalyse starten“ oder „Probleme auf meiner Seite beheben“.
---

# RankM8 Site Audit

## Zweck

Führe ein technisches SEO-Audit für eine RankM8-Seite durch, frage den asynchronen Job ab, werte
die neuesten Ergebnisse aus, sortiere die Probleme, begleite die Behebung und aktualisiere den Status
von Issues oder Checks, sobald der Nutzer bestätigt, dass die Arbeit erledigt ist.

Ein Audit läuft pro Seite, nicht für die ganze Website. Verwende `sitemap_id` für die
gewählte Seite und `audit_id` für das zurückgegebene Audit-Ergebnis.

Erkläre Fachbegriffe beim ersten Vorkommen in einfachen Worten, der Nutzer ist oft Einsteiger.
Das gilt zum Beispiel für Core Web Vitals (Messwerte für Ladegeschwindigkeit und Stabilität einer Seite),
Meta-Daten (Titel und Beschreibung einer Seite in den Suchergebnissen) und Überschriftenstruktur.

## Benötigte MCP-Tools

- `trigger_site_audit(project_id, sitemap_id)`
- `get_site_audit_status(project_id, sitemap_id)`
- `get_site_audit(project_id, sitemap_id?, audit_id?)`
- `list_site_audits(project_id, sitemap_id, limit?, offset?)`
- `list_audit_issues(project_id, audit_id, status?, severity?, limit?, offset?)`
- `update_audit_issue_status(project_id, issue_id, status)`
- `get_audit_checks(project_id, audit_id, status?, severity?)`
- `update_check_status(project_id, check_id, status)`
- `get_audit_images(project_id, audit_id, status?, limit?, offset?)`
- `get_audit_headings(project_id, audit_id, level?, limit?, offset?)`
- `list_project_audit_issues(project_id)`

Hilfreiche Auswahl-Tools:

- `list_projects(limit?, offset?)`
- `list_pages(project_id, search?, limit?, offset?)`

## Ablauf

1. Ermittle das Projekt mit `list_projects()`, falls die `project_id` unbekannt ist.
2. Ermittle die Seite mit `list_pages(project_id, search?, limit?, offset?)`, falls die
   `sitemap_id` unbekannt ist.
3. Bestätige mit dem Nutzer das genaue Projekt und die genaue Seite, bevor du das Audit startest.
4. Erkläre, dass `trigger_site_audit` Credits verbraucht, und hole eine ausdrückliche Freigabe ein.
5. Rufe `trigger_site_audit(project_id, sitemap_id)` auf.
6. Speichere eine zurückgegebene `job_id` und die gewählte `sitemap_id`.
7. Frage `get_site_audit_status(project_id, sitemap_id)` alle 15-30 Sekunden ab.
8. Frage so lange weiter ab, bis der neueste Job einen Endstatus hat. Endstatus von Jobs sind
   `completed`, `failed`, `cancelled` und `insufficient_tokens`.
9. Stoppe bei `failed`, `cancelled` oder `insufficient_tokens`, fasse den Zustand zusammen und
   frage den Nutzer, bevor du es erneut versuchst.
10. Rufe nach `completed` `get_site_audit(project_id, sitemap_id)` auf, um den neuesten Score,
    die Meta-Daten, die Core Web Vitals, die Ressourcen und die Zusammenfassung zu erhalten.
11. Speichere die zurückgegebene `audit_id` für alle Detail-Aufrufe.
12. Rufe `list_audit_issues(project_id, audit_id, limit=50, offset=0)` auf und blättere
    durch weitere Ergebnisse, wenn `total` die erste Seite übersteigt.
13. Rufe optional `get_audit_checks(project_id, audit_id, status?, severity?)` auf,
    um Belege auf Check-Ebene zu erhalten.
14. Rufe optional `get_audit_images(project_id, audit_id, status?, limit?, offset?)` auf, um
    Bildprobleme zu sehen.
15. Rufe optional `get_audit_headings(project_id, audit_id, level?, limit?, offset?)` auf, um
    Probleme in der Überschriftenstruktur zu sehen.
16. Gruppiere die Befunde nach Schweregrad, Auswirkung und Verantwortlichem für die Behebung.
    Markiere nichts als gelöst, nur weil es besprochen wurde.
17. Gib dem Nutzer konkrete Anweisungen zur Behebung. Wenn Änderungen an Code oder Inhalten
    zum Auftrag gehören, führe sie mit den üblichen Datei- oder Website-Tools des Clients aus.
18. Rufe nach der Bestätigung des Nutzers, dass eine Behebung abgeschlossen ist,
    `update_audit_issue_status(project_id, issue_id, status="resolved")` oder
    `update_check_status(project_id, check_id, status="resolved")` auf.
19. Verwende `status="ignored"` nur, wenn der Nutzer das Problem ausdrücklich als gewollt
    oder irrelevant akzeptiert.
20. Rufe zum Schluss `list_project_audit_issues(project_id)` auf, um die verbleibenden
    projektweiten Probleme zusammenzufassen.

Nenne dem Nutzer am Ende die drei wichtigsten Probleme zuerst, in einfacher Sprache und mit dem erwarteten Nutzen der Behebung.

## Polling

Verwende nur `get_site_audit_status(project_id, sitemap_id)` für den Live-Fortschritt des Audits.
Die `SiteAudit`-Entität existiert unter Umständen erst, wenn der Import abgeschlossen ist. Frage
deshalb `get_site_audit` nicht für den Fortschritt ab.

Frage alle 15-30 Sekunden ab oder folge einem zurückgegebenen Intervall, falls vorhanden. Beobachte
`job.is_terminal`, `job.status`, Fortschrittsfelder, die Zusammenfassung des neuesten Audits und
`warning`. Eine Warnung nach langer Laufzeit bedeutet, dass der Job veraltet sein kann. Stoppe dann und frage
nach, bevor du ein weiteres kostenpflichtiges Audit startest.

## Bestätigung durch den Nutzer

Hole immer eine ausdrückliche Bestätigung des Nutzers ein, bevor du:

- ein Projekt auswählst, wenn mehrere Projekte passen.
- die zu prüfende Seite auswählst, wenn mehrere Seiten passen.
- `trigger_site_audit` aufrufst, weil es Credits verbraucht.
- ein fehlgeschlagenes oder veraltetes Audit wiederholst, weil dadurch ein weiterer DataForSEO-Task entstehen kann.
- den Status von Issues oder Checks auf `resolved` oder `ignored` setzt.

In diesem Audit-Workflow gibt es keine MCP-Löschoperation. Erfinde keine.

## Fehlerbehandlung

- `audit_already_running`: Löse nicht erneut aus, sondern frage den bestehenden Job weiter ab.
- `insufficient_credits`: Stoppe und bitte den Nutzer, Credits aufzuladen, bevor du es erneut versuchst.
- `audit_not_found`: Prüfe, ob das Audit abgeschlossen wurde. Löse andernfalls
  nur nach Bestätigung ein Audit aus.
- `issue_not_found` oder `check_not_found`: Lade Issues bzw. Checks neu und verwende IDs
  aus dem aktuellen Projekt.
- `invalid_status`: Verwende nur `open`, `in_progress`, `resolved` oder `ignored`.
- `invalid_sitemap_ids`: Führe `list_pages` erneut aus und wähle eine gültige Seiten-ID.
- `missing_audit_reference`: Rufe `get_site_audit(project_id, sitemap_id)` auf oder übergib
  eine gültige `audit_id`.
- `dispatch_failed`: Stoppe und frage, bevor du das Auslösen wiederholst.
- `project_not_found`: Wähle das Projekt neu aus oder bitte den Nutzer, die OAuth-Verbindung neu herzustellen.

## Passt zusammen mit

- Davor: `rankm8-project-setup`, damit Projekt und Seiten in RankM8 vorhanden sind. Für eine fremde Website ohne Projekt: `seo-schnellcheck`.
- Gefundene Probleme beheben: `seo-umsetzen` (Änderungen auf der Website umsetzen).
- Danach: `rankm8-reporting` für den Monatsbericht, `rankm8-content-pipeline` für Inhalte, die Lücken schließen.
- Begriffe nachlesen: `seo-grundlagen`.
