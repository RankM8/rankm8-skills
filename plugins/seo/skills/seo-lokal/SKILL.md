---
name: seo-lokal
description: "Macht einen lokalen Betrieb in seiner Stadt sichtbar: Google-Unternehmensprofil, Kartenbereich (Local Pack), Bewertungen, Standort- und Leistungsseiten, einheitliche Firmendaten. Verwenden bei „lokales SEO“, „in meiner Stadt gefunden werden“, „Google Maps“, „Google-Unternehmensprofil“, „Google My Business“, „Bewertungen bekommen“, „Local Pack“, „Standortseiten“ oder bei Betrieben mit festem Einzugsgebiet."
---

# Lokales SEO

Für Praxen, Handwerk, Gastro, Studios, Kanzleien und alle mit Einzugsgebiet. Der schnellste Hebel
ist fast immer das Google-Unternehmensprofil, nicht die Website.

## 1. Lage messen

- **Mit RankM8-Projekt** (lokales Projekt, `project_scope="local"`):
  - `get_gmb_data(project_id, date_range="28d", include_keywords=true)`: Aufrufe, Anrufe,
    Routen, Website-Klicks und Suchbegriffe aus dem Unternehmensprofil. `connected=false` heißt:
    Profil in der RankM8-App unter den Projekt-Einstellungen verbinden.
  - Je wichtiger Leistung: `serp_search(query="<leistung> <ort>", project_id=<id>)` (Credits bei
    Live-Abruf): Platz in der normalen Suche, im Kartenbereich ja/nein, wer dort steht.
- **Ohne Projekt:** `seo-schnellcheck` mit `focus_keyword` und `place`.
- 2 bis 3 Suchen wählen, die Geld bringen („Zahnarzt Köln Ehrenfeld“, „Notdienst Zahnarzt Köln“),
  nicht nur den Firmennamen.

## 2. Google-Unternehmensprofil (der Nutzer pflegt es selbst unter business.google.com)

Checkliste, je Punkt kurz prüfen lassen und erklären:

1. **Hauptkategorie** trifft die Kernleistung genau; passende Zusatzkategorien.
2. **Name** wie im echten Leben, ohne angehängte Suchbegriffe (verstößt gegen Googles Richtlinien).
3. **Adresse, Telefon, Öffnungszeiten** vollständig und identisch mit Website und Verzeichnissen.
4. **Leistungen/Produkte** mit kurzen Beschreibungen eingetragen.
5. **Fotos:** echte Fotos von Team, Räumen, Arbeit; regelmäßig neue.
6. **Beiträge:** etwa alle zwei Wochen ein kurzer Beitrag (Angebot, Neuigkeit, Tipp).
7. **Fragen und Antworten** gepflegt.
8. **Website-Link** auf die passende Seite (bei mehreren Standorten die Standortseite).

## 3. Bewertungen

- Aktiv, aber ehrlich bitten: nach erfolgreichem Auftrag, mit direktem Bewertungslink, per Mail,
  Karte oder QR-Code.
- Jede Bewertung beantworten, auch kritische: sachlich, ohne Patientendaten oder Details.
- **Nie** Bewertungen kaufen, selbst schreiben, Gegenleistungen dafür geben oder nur zufriedene Kunden
  gezielt fragen (Review-Gating): verstößt gegen Googles Richtlinien und kann wettbewerbswidrig
  sein. Bei Heilberufen zusätzlich Werberecht beachten.

## 4. Website lokal stärken

- Ort und Leistung in Seitentitel, Hauptüberschrift und Text der Startseite und Leistungsseiten.
- **Eine Seite je Kernleistung**, bei mehreren Standorten **eine Seite je Standort** mit echtem,
  eigenem Inhalt (Adresse, Team, Anfahrt, Besonderheiten). Keine Kopien mit getauschtem Ortsnamen.
- Strukturierte Daten `LocalBusiness` (oder genauer, z. B. `Dentist`) mit Name, Adresse, Telefon,
  Öffnungszeiten, Geo-Koordinaten: Umsetzung in `seo-umsetzen`.
- Name, Adresse, Telefon überall gleich schreiben: Website, Impressum, Profil, Branchenbücher.

## 5. Ergebnis

Kurzplan mit höchstens fünf Schritten, sortiert nach Wirkung: zuerst Profil, dann Bewertungen, dann
Website. Je Schritt: was, wer, wie lange. Nach 4 bis 8 Wochen erneut messen (Schritt 1).

## Passt zusammen mit

- Ortsbezogene Suchbegriffe finden: `rankm8-keyword-research`.
- Änderungen auf der Website einbauen: `seo-umsetzen`.
- Monatlich Profilzahlen berichten: `rankm8-reporting`.
