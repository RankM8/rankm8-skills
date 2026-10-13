---
name: seo-geo
description: "Macht eine Website für KI-Suchmaschinen sichtbar (GEO): prüft, ob ChatGPT, Claude, Perplexity und Google-KI die Seite lesen dürfen, schreibt eine fertige llms.txt, empfiehlt strukturierte Daten und zitierbare Abschnitte. Verwenden bei „GEO“, „in ChatGPT auftauchen“, „KI-Sichtbarkeit“, „Perplexity“, „Google AI Overviews“, „KI-Übersicht“, „llms.txt“, „KI-Crawler“, „robots.txt für KI“ oder „wird meine Firma von KI empfohlen“."
---

# Sichtbarkeit in KI-Antworten (GEO)

KI-Suchmaschinen nennen Quellen, die sie lesen dürfen, verstehen und zitieren können. Grundlage
bleibt normales SEO: Was bei Google nicht gefunden wird, nennt auch die KI selten. Ehrlich bleiben:
Niemand kann eine Nennung in ChatGPT garantieren, und RankM8 misst KI-Nennungen (noch) nicht.

## 1. Zugang prüfen: robots.txt

Quelle: `seo-schnellcheck` (`result.ai_crawlers`) oder die Datei `https://<domain>/robots.txt`
selbst (in Claude Code `curl -sL`, sonst im Browser öffnen lassen).

| Gruppe | Bots | Gesperrt heißt |
|---|---|---|
| Antwort- und Such-Bots | `OAI-SearchBot`, `ChatGPT-User`, `Claude-SearchBot`, `Claude-User`, `PerplexityBot`, `Perplexity-User` | Die Seite kann in KI-Antworten kaum als Quelle erscheinen. **Das ist der Hebel.** |
| Trainings-Bots | `GPTBot`, `ClaudeBot`, `CCBot`, `Applebot-Extended`, `meta-externalagent` | Inhalte fließen nicht ins Training. Bewusste Entscheidung, kein Fehler. |
| `Google-Extended` | kein eigener Crawler, ein Schalter | Steuert die Nutzung für Gemini, nicht die normale Google-Suche. |
| Klassische Suche | `Googlebot`, `Bingbot` | Bing speist auch Copilot und Teile der ChatGPT-Suche. Nie sperren. |

Wichtig: Ein Bot hält sich nur an seine eigene Gruppe. Steht er nicht namentlich drin, gilt
`User-agent: *`. Ein `Disallow: /` für alle sperrt also auch alle KI-Bots.

Empfehlung für die meisten Betriebe: Antwort- und Such-Bots erlauben; Trainings-Bots nach eigener
Haltung. Beispiel-Block, wenn der Nutzer Training ausschließen, Antworten aber erlauben will:

```
User-agent: GPTBot
Disallow: /

User-agent: CCBot
Disallow: /

User-agent: *
Allow: /
```

## 2. Lesbarkeit

- Inhalte müssen ohne JavaScript im HTML stehen. Viele KI-Bots führen kein JavaScript aus
  (Schnellcheck-Befund `client_rendered`). Abhilfe: serverseitiges Rendern oder statische Seiten.
- Wichtige Fakten als Text, nicht nur im Bild oder PDF.

## 3. Verständlichkeit: strukturierte Daten

- `Organization` bzw. `LocalBusiness` (oder genauer, z. B. `Dentist`, `LegalService`) mit Name,
  Adresse, Telefon, Logo, Öffnungszeiten, `sameAs` (Profile).
- Leistungsseiten: `Service`; Artikel: `Article` mit Autor und Datum.
- `FAQPage` nur für echte Fragen auf der Seite; Google zeigt FAQ-Rich-Results kaum noch an, für das
  Verständnis bleibt es nützlich. Nichts versprechen.
- Keine Bewertungssterne (`aggregateRating`) für das eigene Unternehmen eintragen: Google wertet das
  als Selbstbewertung.
- Umsetzung: `seo-umsetzen`.

## 4. Zitierbarkeit

- Jede wichtige Seite beantwortet ihre Kernfrage in den ersten zwei, drei Sätzen.
- Klare Zwischenüberschriften als Fragen, kurze Absätze, Listen, konkrete Zahlen mit Quelle.
- Eigene Daten, Preise, Abläufe, Erfahrungswerte: Das hat sonst niemand und wird deshalb zitiert.
- Name, Ort, Leistung einheitlich auf allen Seiten und Profilen (Konsistenz hilft der Zuordnung).
- Erwähnungen auf anderen Websites (Branchenportale, Presse, Verbände) stärken die Nennung.

## 5. llms.txt schreiben

Ein junger Standard, der KI-Werkzeugen die Website zusammenfasst. Kein belegter Ranking-Faktor, aber
schnell gemacht. Ehrlich formulieren: „hilft KI-Werkzeugen, die Seite richtig zu verstehen“.

Aufbau (Markdown, Datei `/llms.txt` im Hauptverzeichnis):

```
# <Firmenname>

> <Ein Satz: was die Firma macht, für wen, wo.>

<Zwei, drei Sätze mit Besonderheiten. Nur belegte Angaben.>

## Leistungen
- [<Leistung>](<https://domain/leistung>): <ein Satz>

## Über uns
- [Team](<https://domain/team>): <ein Satz>

## Kontakt
- [Kontakt](<https://domain/kontakt>): <Adresse, Telefon>
```

Nur Seiten aufnehmen, die es gibt; Angaben von der Website nehmen, nichts erfinden.

## 6. Prüfen

- Nach der Umsetzung: `seo-schnellcheck` erneut (KI-Crawler und `llms.txt`).
- Stichprobe von Hand: in ChatGPT-Suche und Perplexity 3 typische Fragen stellen („bester Zahnarzt
  in Köln Ehrenfeld“). Ergebnis als Momentaufnahme dokumentieren; Antworten schwanken stark.

## Passt zusammen mit

- Ausgangslage messen: `seo-schnellcheck`.
- robots.txt, strukturierte Daten und llms.txt einbauen: `seo-umsetzen`.
- Inhalte zitierbar schreiben: `rankm8-content-pipeline`.
- Lokale Betriebe: `seo-lokal`.
