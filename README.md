# RankM8 Skills

Workflow-Skills für [RankM8](https://app.rankm8.com) — SEO-Analyse und Content-Optimierung über das Model Context Protocol (MCP). Die Skills orchestrieren komplette SEO-Workflows über die 53 MCP-Tools des RankM8-Servers: Projekte anlegen und einrichten, technische Site-Audits fahren, Keywords recherchieren und verwalten, Content generieren und Performance-Reports erstellen.

## Setup (2 Befehle)

```bash
# 1. RankM8-MCP verbinden (OAuth-Autorisierung öffnet sich im Browser)
claude mcp add --transport http rankm8 https://app.rankm8.com/_mcp/v1

# 2. Skills installieren
npx skills add RankM8/rankm8-skills --all
```

Fertig — die Skills nutzen die MCP-Verbindung, es ist kein weiteres Login und keine CLI nötig. Ein RankM8-Konto wird vorausgesetzt.

## Die Skills

| Skill | Was er tut | MCP-Tools |
|-------|------------|-----------|
| `rankm8-project-setup` | Legt aus einer URL ein komplettes Projekt an: Website-Analyse, Seiten-Discovery, Crawl, Keywords mit Suchvolumen, Analyse, Konkurrenten | 10 |
| `rankm8-site-audit` | Fährt technische SEO-Audits pro Seite, pollt den Job, priorisiert Issues und führt durch die Behebung | 11 |
| `rankm8-content-pipeline` | Analyse-Plan → Outline → fertiger Artikel, inkl. Meta-Tags und AI-Bildern | 7 |
| `rankm8-keyword-research` | Findet Keyword-Chancen mit echtem Suchvolumen und SERP-Konkurrenzanalyse, importiert und verknüpft sie | 8 |
| `rankm8-reporting` | Fasst Search-Console- und Google-Business-Daten zu einem Monats-Report zusammen | 4 |

Typischer Ablauf für ein neues Projekt: `rankm8-project-setup` → `rankm8-site-audit` → `rankm8-keyword-research` → `rankm8-content-pipeline` → monatlich `rankm8-reporting`.

### Einzelne Skills installieren

```bash
npx skills add RankM8/rankm8-skills --skill rankm8-site-audit
npx skills add RankM8/rankm8-skills --list        # nur anzeigen, nichts installieren
npx skills add RankM8/rankm8-skills --all -g      # global statt projektweit
```

## Credits

MCP hat kein Plan-Limit — **Credits** sind die einzige Nutzungsbremse. Kostenlos sind alle Lese- und Verwaltungs-Tools (Projekte, Seiten, Keywords, Audit-Ergebnisse, Integrations-Daten). Credits verbrauchen: Website-Analyse, Sitemap-Scrape, Seiten-Crawl, Keyword-Metriken und -Analyse, Site-Audit, SERP-/Keyword-Research und alle Content-/Bild-Generierungen. Die Skills fragen vor jedem kostenpflichtigen Schritt nach.

## Clients ohne Skill-Support

Alle MCP-Clients (ChatGPT, Gemini, Cursor, Windsurf, VS Code, JetBrains …) können den RankM8-Server ohne Skills nutzen — die Tools sind selbstbeschreibend. Setup-Anleitungen für alle Plattformen: in der App unter **`/mcp`**.

## Hinweise

- Die Skills sind client-agnostisch und beschreiben nur MCP-Tool-Aufrufe — sie funktionieren mit Claude Code, Cursor, Codex und allen weiteren von [skills.sh](https://skills.sh) unterstützten Agents.
- Destruktive Aktionen (Keywords/Seiten löschen) verlangen serverseitig eine explizite Bestätigung; die Skills holen sie vorher beim Nutzer ein.
- Änderungen an den Skills laufen über das RankM8-Hauptrepo und werden hierher gespiegelt.
