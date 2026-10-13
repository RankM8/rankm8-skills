# RankM8 Skills

Plugin-Marketplace **`rankm8`** mit dem Plugin **`seo`**: SEO und Sichtbarkeit in KI-Antworten mit
[RankM8](https://app.rankm8.com), für Einsteiger und Profis. Die Skills steuern den RankM8-Server
über das Model Context Protocol (MCP): Websites in Minuten prüfen, Projekte einrichten, technische
Fehler finden, Suchbegriffe recherchieren, Texte erstellen, lokal und in KI-Antworten sichtbar
werden, Monatsberichte schreiben und SEO als Dienstleistung verkaufen.

Ein RankM8-Konto wird vorausgesetzt.

## Installation

**Claude Code (empfohlen, mit Updates und RankM8-Verbindung):**

```bash
/plugin marketplace add RankM8/rankm8-skills
/plugin install seo@rankm8
```

Danach `/mcp` → `rankm8` → anmelden, dann den Skill **`seo-setup`** aufrufen. Updates: `/plugin` →
Marketplaces → `rankm8` → Auto-Update einschalten.

**Codex:** Marketplace `RankM8/rankm8-skills` hinzufügen und unter `/plugins` installieren.

**Cursor, Windsurf und andere Agenten (nur Skills):**

```bash
# 1. RankM8-MCP verbinden (Anleitung je Werkzeug in der App unter /mcp), z. B. Claude Code ohne Plugin:
claude mcp add --transport http rankm8 https://app.rankm8.com/_mcp/v1

# 2. Alle Skills installieren (immer die neueste Version aus dem Plugin)
npx skills add RankM8/rankm8-skills --all
```

Einzelne Skills:

```bash
npx skills add RankM8/rankm8-skills --skill rankm8-site-audit
npx skills add RankM8/rankm8-skills --list        # nur anzeigen, nichts installieren
npx skills add RankM8/rankm8-skills --all -g      # global statt projektweit
```

Wer von `npx skills` auf das Plugin umsteigt, entfernt die alten Kopien (der Skill `seo-setup` hilft
dabei), sonst sind die Skills doppelt vorhanden.

**claude.ai / Claude App:** Einstellungen → Connectors → eigenen Connector mit der Adresse
`https://app.rankm8.com/_mcp/v1`. Die Tools beschreiben sich selbst; Skills lassen sich zusätzlich
als Datei hochladen.

## Die Skills

**Einstieg und Wissen**

| Skill | Was er tut |
|-------|------------|
| `seo` | Einstieg: fragt das Ziel ab und leitet an den passenden Skill weiter |
| `seo-setup` | RankM8-Verbindung prüfen, alte Kopien aufräumen, Credits erklären |
| `seo-grundlagen` | SEO und KI-Sichtbarkeit einfach erklärt, mit Glossar |

**Prüfen und Einrichten**

| Skill | Was er tut | Credits |
|-------|------------|---------|
| `seo-schnellcheck` | Jede Website ohne Projekt prüfen; privater Report-Link für den Inhaber | ja |
| `rankm8-project-setup` | Projekt aus einer URL komplett einrichten: Seiten, Crawl, Suchbegriffe, Analyse, Mitbewerber | ja |
| `rankm8-site-audit` | Technische Audits pro Seite, Probleme priorisieren, Status pflegen | ja |

**Verbessern**

| Skill | Was er tut | Credits |
|-------|------------|---------|
| `rankm8-keyword-research` | Suchbegriffe mit echtem Suchvolumen finden, importieren, analysieren, Seiten zuordnen | ja |
| `rankm8-content-pipeline` | Analyse-Plan → Gliederung → fertiger Text, mit Meta-Angaben und Bildern | ja |
| `seo-lokal` | Google-Unternehmensprofil, Kartenbereich, Bewertungen, Standortseiten | teils |
| `seo-geo` | In ChatGPT, Perplexity und Google-KI sichtbar werden: KI-Crawler, strukturierte Daten, `llms.txt` | teils |
| `seo-umsetzen` | Verbesserungen im Code oder im Baukasten einbauen, Audit-Punkte abhaken | teils |

**Berichten und Verkaufen**

| Skill | Was er tut | Credits |
|-------|------------|---------|
| `rankm8-reporting` | Monatsbericht aus Search Console, Unternehmensprofil und Suchbegriffen | nein |
| `seo-verkaufen` | SEO-Pakete schnüren, Preis kalkulieren, Angebot schreiben, Erstkontakt | nein |

Typischer Ablauf für eine eigene Website: `seo-schnellcheck` → `rankm8-project-setup` →
`rankm8-site-audit` → `seo-umsetzen` → `rankm8-keyword-research` → `rankm8-content-pipeline` →
monatlich `rankm8-reporting`. Lokale Betriebe zusätzlich `seo-lokal`.

## Credits

MCP hat kein Plan-Limit; **Credits** sind die einzige Nutzungsbremse. Kostenlos sind alle Lese- und
Verwaltungs-Tools. Credits verbrauchen: Website-Analyse, Seiten finden und lesen, Suchbegriff-Daten
und -Analyse, Site-Audit, Schnellcheck, Live-Suchergebnisse und alle Text- und Bild-Erstellungen. Die
Skills fragen vor jedem kostenpflichtigen Schritt nach.

## Aufbau

```
.claude-plugin/marketplace.json     Marketplace „rankm8“ (Claude Code)
.agents/plugins/marketplace.json    Marketplace für Codex
plugins/seo/
├── .claude-plugin/plugin.json      Plugin-Manifest (Claude Code)
├── plugin.json                     portables Manifest
├── .mcp.json, mcp.json             RankM8-Verbindung
└── skills/<skill>/SKILL.md         alle Skills (Quelle für Plugin und npx skills)
```

## Hinweise

- Alle Skills nutzen nur die Agent-Skills-Standardfelder und funktionieren in Claude Code, Codex,
  Cursor und allen von [skills.sh](https://skills.sh) unterstützten Agenten.
- Destruktive Aktionen (Suchbegriffe oder Seiten löschen) verlangen serverseitig eine ausdrückliche
  Bestätigung; die Skills holen sie vorher ein.
- Dieses Repo ist die Quelle der Skills. Die RankM8-App verlinkt von der Seite `/mcp` hierher.
