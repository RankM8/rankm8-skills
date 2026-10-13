---
name: seo-umsetzen
description: "Baut SEO-Verbesserungen direkt auf der Website ein: Seitentitel und Beschreibungen, Überschriften, Alt-Texte, strukturierte Daten (JSON-LD), robots.txt, llms.txt, Sitemap, Weiterleitungen, im Code (HTML, Astro, Next.js) oder als Klick-Anleitung für WordPress, Wix, Jimdo, Webflow und andere Baukästen. Verwenden bei „SEO umsetzen“, „bau das ein“, „Title ändern“, „Meta-Beschreibung einbauen“, „JSON-LD einbauen“, „Schema einbauen“, „Audit-Fehler beheben“, „robots.txt anpassen“ oder nach einem Schnellcheck oder Site-Audit."
---

# SEO umsetzen

Aus Befunden werden Änderungen. Nichts live ändern ohne Freigabe; vorher sichern.

## 1. Aufgabenliste

Quelle der Befunde:

- `rankm8-site-audit`: `list_audit_issues(project_id, audit_id)` und `get_audit_checks(...)`;
  projektweit `list_project_audit_issues(project_id)`.
- `seo-schnellcheck`: `findings` aus `get_quick_check`.
- Empfehlungen aus `seo-geo` oder `seo-lokal`.

Liste nach Wirkung sortieren (Sperren wie `noindex` zuerst, dann Titel/Beschreibung, dann der Rest)
und mit dem Nutzer bestätigen.

## 2. Wo liegt die Website?

Fragen und den Weg wählen:

| Website | Weg |
|---|---|
| Code im Projektordner (HTML, Astro, Next.js, Hugo …), Claude Code | Direkt im Code ändern, Diff zeigen, nach Freigabe speichern |
| WordPress | Klick-Anleitung; Titel/Beschreibung über das SEO-Plugin (Yoast, Rank Math, SEOPress); bei Zugriff auf das Theme auch Code |
| Wix, Jimdo, Webflow, Squarespace, Shopify, andere Baukästen | Klick-Anleitung für die SEO-Einstellungen der jeweiligen Seite |
| Keine Ahnung / Agentur betreut | Fertige Texte und eine Liste für den Betreuer |

Ohne Dateizugriff (claude.ai): fertige Texte und Code-Blöcke zum Kopieren liefern.

## 3. Bausteine

**Seitentitel und Meta-Beschreibung**
- Titel 50 bis 60 Zeichen: Leistung, Ort (wenn lokal), Marke. Jede Seite einen eigenen.
- Beschreibung 140 bis 160 Zeichen: Nutzen und Aufforderung. Nur belegte Aussagen.
- Mit RankM8-Projekt: `generate_meta_info(project_id, page_ids=[...], keyword_ids=[...])` schreibt
  Vorschläge (Credits, vorher fragen).

**Überschriften und Text**
- Genau eine H1 mit dem Hauptthema, darunter H2/H3 in logischer Reihenfolge.
- Kernfrage in den ersten Sätzen beantworten.

**Bilder**
- Alt-Text beschreibt, was zu sehen ist, natürlich formuliert, ohne Keyword-Stapel. Dekorative
  Bilder mit leerem `alt=""`.
- Daten aus dem Audit: `get_audit_images(project_id, audit_id, status="needs_alt_text")`.

**Strukturierte Daten (JSON-LD)**
- In den `<head>` als `<script type="application/ld+json">`. Typen siehe `seo-geo`.
- Nur Angaben, die auf der Seite sichtbar sind; keine Eigenbewertungen (`aggregateRating`).
- Prüfen lassen mit dem Google-Test für Rich-Suchergebnisse.

**robots.txt, llms.txt, Sitemap**
- `robots.txt` und `llms.txt` ins Hauptverzeichnis (Inhalt aus `seo-geo`). Bei Baukästen oft in
  den SEO-Einstellungen; manche erlauben keine eigene `llms.txt`, dann weglassen.
- Sitemap unter `/sitemap.xml`, in `robots.txt` mit `Sitemap: https://<domain>/sitemap.xml` nennen
  und in der Search Console einreichen.

**Technik**
- `noindex` von wichtigen Seiten entfernen; Canonical auf die eigene Adresse.
- Geänderte Adressen per 301-Weiterleitung auf die neue Adresse.
- HTTPS erzwingen, `viewport`-Meta-Tag setzen.

## 4. Nach der Umsetzung

1. Live prüfen: Seite im Browser, Quelltext auf Titel, Beschreibung, JSON-LD.
2. Mit Projekt: `trigger_site_audit(project_id, sitemap_id)` für die geänderte Seite (Credits,
   vorher fragen), sonst `seo-schnellcheck`.
3. Erst wenn der Nutzer bestätigt, dass es live ist: `update_audit_issue_status(project_id,
   issue_id, status="resolved")` bzw. `update_check_status(project_id, check_id, status="resolved")`.
   `ignored` nur, wenn der Nutzer einen Punkt bewusst so lassen will.
4. Wichtige Seiten in der Search Console neu zur Indexierung anmelden.

## Leitplanken

- Vor Änderungen am Live-System sichern (Backup, Git-Commit oder Kopie der alten Texte).
- Keine Texte erfinden: Leistungen, Preise, Orte nur aus der Website oder vom Nutzer.
- Bei WordPress keine Plugins installieren lassen, ohne den Nutzer auf Updates und Sicherheit
  hinzuweisen.

## Passt zusammen mit

- Befunde holen: `rankm8-site-audit`, `seo-schnellcheck`.
- Inhalte für neue Seiten: `rankm8-content-pipeline`.
- Wirkung messen: `rankm8-reporting`.
