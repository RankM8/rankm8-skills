---
name: rankm8-content-pipeline
description: "Erstellt SEO-Inhalte mit den RankM8-MCP-Generierungstools in kontrollierter Reihenfolge: Analyseplan, Gliederung, Text, Meta-Informationen und optional KI-Bilder. Greift bei „Text schreiben“, „Blogartikel erstellen“, „SEO-Content erstellen“, „Gliederung erstellen“, „Meta-Titel und Beschreibung“, „Bild generieren“ oder „Content Pipeline“."
---

# RankM8 Content-Erstellung

## Zweck

Erstelle SEO-Inhalte aus RankM8-Projektdaten in einer kontrollierten Reihenfolge:
Analyseplan, Gliederung, Text, Meta-Informationen und optional Bilderzeugung.
Nutze wann immer möglich echte Seiten, Keywords, SERPs und Mitbewerber-Kontext.

Erkläre Fachbegriffe beim ersten Vorkommen in einfachen Worten, der Nutzer ist oft Einsteiger.
Das gilt zum Beispiel für Gliederung (Aufbau des Textes mit Überschriften), Meta-Informationen (Titel und Beschreibung
in den Suchergebnissen) und `keyword_fit` (wie gut ein Keyword zur Website passt).

## Benötigte MCP-Tools

- `generate_analyse_plan(project_id, prompt?, model?, page_ids?, keyword_ids?, settings?)`
- `generate_outline(project_id, prompt?, model?, page_ids?, keyword_ids?, plan_id?, settings?)`
- `generate_content(project_id, prompt?, model?, page_ids?, keyword_ids?, plan_id?, outline_id?, settings?)`
- `generate_full_content(project_id, prompt?, model?, page_ids?, keyword_ids?, settings?)`
- `generate_meta_info(project_id, content_id?, prompt?, model?, page_ids?, keyword_ids?)`
- `generate_image(prompt, project_id, reference_image_base64?, reference_image_urls?, reference_image_ids?, model?)`
- `get_generation_status(entity_type, entity_id)`

- `list_projects(limit?, offset?)`
- `list_pages(project_id, search?, limit?, offset?)`
- `search_keywords(project_id, query?, limit?)`
- `get_keyword_details(keyword_id)`
- `keyword_research(keyword, type, project_id?, language?, location_code?, include_serp_info?, limit?)`
- `analyze_keywords(project_id, keyword_ids)`
- `list_models(category?, capability?)`

## Ablauf

1. Ermittle und bestätige die `project_id`. Wähle für die Generierung nie automatisch ein Projekt aus.
2. Frage den Nutzer nach Thema, Zielseite, Ziel-Keywords, gewünschter Länge und
   Tonalität, falls sie nicht angegeben wurden.
3. Nutze `list_pages(project_id, search?, limit?, offset?)`, um Zielseiten zu finden
   und die `page_ids` zu bestätigen.
4. Nutze `search_keywords(project_id, query?, limit?)`, um Ziel-Keywords zu finden
   und die `keyword_ids` zu bestätigen.
5. Prüfe für jedes Ziel-Keyword `keyword_fit`. Ist der Wert `null`, frage, ob vor dem Schreiben
   die Datenqualität verbessert werden soll.
6. Führe bei Zustimmung `analyze_keywords(project_id, keyword_ids=[...])` aus und
   frage `get_keyword_details(keyword_id)` alle 30 Sekunden ab, bis `keyword_fit`
   nicht mehr `null` ist oder der Nutzer verlangt, ohne vollständige Analyse fortzufahren.
7. Rufe optional `list_models(category="chat")` auf, wenn der Nutzer ein Modell oder
   einen Kompromiss zwischen Qualität und Kosten wünscht.
8. Bestätige die Credit-Nutzung für `generate_analyse_plan`.
9. Rufe `generate_analyse_plan(project_id, prompt?, model?, page_ids?,
   keyword_ids?, settings?)` auf.
10. Frage `get_generation_status(entity_type="plan", entity_id=writing_plan_id)`
    alle 30 Sekunden ab, bis `completed` oder `failed` erreicht ist.
11. Prüfe den fertigen Plan gemeinsam mit dem Nutzer, bevor du eine Gliederung erstellst.
12. Bestätige die Credit-Nutzung für `generate_outline`.
13. Rufe `generate_outline(project_id, prompt?, model?, page_ids?, keyword_ids?,
    plan_id=writing_plan_id, settings?)` auf.
14. Frage `get_generation_status(entity_type="outline",
    entity_id=writing_outline_id)` alle 30 Sekunden ab, bis `completed` oder `failed` erreicht ist.
15. Prüfe die Gliederung mit dem Nutzer und hole seine Freigabe ein, bevor der vollständige Text entsteht.
16. Bestätige die Credit-Nutzung für `generate_content`.
17. Rufe `generate_content(project_id, prompt?, model?, page_ids?, keyword_ids?,
    plan_id=writing_plan_id, outline_id=writing_outline_id, settings?)` auf.
18. Frage `get_generation_status(entity_type="content",
    entity_id=writing_content_id)` alle 30 Sekunden ab, bis `completed` oder `failed` erreicht ist.
19. Bestätige die Credit-Nutzung für `generate_meta_info`.
20. Rufe `generate_meta_info(project_id, content_id=writing_content_id, prompt?,
    model?, page_ids?, keyword_ids?)` auf. Der Aufruf ist synchron, Polling ist nicht nötig.
21. Wenn ein Bild gewünscht ist, frage nach Bildrichtung, Referenzen und der abschließenden
    Projektbestätigung.
22. Bestätige die Credit-Nutzung für `generate_image`.
23. Rufe `generate_image(prompt, project_id, reference_image_base64?, reference_image_urls?, reference_image_ids?, model?)` auf.

Lies den fertigen Text vor der Übergabe noch einmal auf Verständlichkeit und Tonalität gegen und weise den Nutzer darauf hin, ihn vor der Veröffentlichung selbst zu prüfen.

## Polling

Verwende für Generierungsjobs immer `get_generation_status(entity_type, entity_id)`.
Nutze `entity_type="plan"` für `writing_plan_id`, `entity_type="outline"` für
`writing_outline_id` und `entity_type="content"` für `writing_content_id`.

Gültige Generierungs-Status sind `pending`, `processing`, `completed` und `failed`.
Frage alle 30 Sekunden ab, bis ein Endstatus erreicht ist. Enthält eine Antwort `poll_again_in_seconds`,
befolge den Wert. Enthält eine Antwort `warning` oder läuft sie mit
`failure_reason="timeout"` in `failed`, stoppe und frage, bevor du einen neuen Job startest.

`generate_full_content` ist eine alternative Pipeline in einem einzigen Aufruf. Sie gibt
`writing_plan_id`, `writing_outline_id` und `writing_content_id` zurück; frage die Content-Entität
für die gesamte Pipeline ab und bei Bedarf Plan und Gliederung für Zwischenergebnisse.

## Bestätigung durch den Nutzer

Bestätige immer, bevor du:

- ein Credit-verbrauchendes Generierungstool aufrufst.
- ein ausgewähltes Projekt für erzeugte Assets oder Inhalte verwendest.
- `analyze_keywords` ausführst, um die Keyword-Qualität zu verbessern.
- vom Plan zur Gliederung und von der Gliederung zum Text übergehst.
- Referenzbilder mit `generate_image` verwendest.

Dieser Workflow enthält keine Lösch-Tools. Wenn der Nutzer erzeugte Inhalte aus RankM8
entfernen möchte, erkläre, dass dieses Playbook keinen Löschschritt anbietet.

## Fehlerbehandlung

- `insufficient_credits`: Stoppe und bitte den Nutzer, Credits aufzuladen oder einen günstigeren Schritt zu wählen.
- `invalid_model`: Rufe `list_models(category="chat")` für Text oder
  `list_models(category="image-generation")` für Bilder auf.
- `project_not_found`: Wähle das Projekt neu aus oder stelle die OAuth-Verbindung neu her.
- `plan_not_found`, `outline_not_found` oder `content_not_found`: Frage erneut ab oder verwende nur IDs
  aus dem aktuellen Projekt.
- `invalid_settings`: Korrigiere `settings`-Werte wie `content_length`,
  `custom_word_count`, `writing_style`, `topical_terms_optimization`,
  `analysis_depth` oder `section_depth`.
- `missing_prompt`: Gib für `generate_image` einen Prompt an.
- `generation_failed` oder `reference_image_failed`: Zeige die Fehlerdetails und wiederhole
  den Aufruf erst, nachdem der Nutzer geänderte Eingaben bestätigt hat.
- `not_found`: Behandle den Fall als fehlenden Zugriff oder veraltete ID; wähle das Artefakt neu aus.

## Passt zusammen mit

- Davor: `rankm8-keyword-research` (Ziel-Keywords festlegen), `rankm8-project-setup` (Projekt und Seiten).
- Texte auf die Website bringen: `seo-umsetzen`.
- Sichtbarkeit in KI-Antworten und strukturierte Daten: `seo-geo`. Lokale Inhalte: `seo-lokal`.
- Danach: `rankm8-reporting`, um die Wirkung zu messen.
