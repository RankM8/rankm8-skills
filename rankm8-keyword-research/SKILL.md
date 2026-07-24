---
name: rankm8-keyword-research
description: Use this skill when a user wants to discover, validate, import, analyze, link, rank, or delete SEO keywords through RankM8 MCP.
---

# RankM8 Keyword Research

## Purpose

Research new keyword opportunities, compare SERPs and competitor domains, import
keywords into RankM8, enrich them with metrics, run analysis, and maintain keyword
to page relationships.

## Required MCP Tools

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

## Step-by-Step Workflow

1. Identify and confirm the `project_id`; use `list_projects()` if needed.
2. Ask for seed keywords, competitor domains, target market, language, and location.
3. Confirm credit usage before each DataForSEO-backed call.
4. For seed expansion, call `keyword_research(keyword, type="suggestions",
   project_id?, language?, location_code?, include_serp_info?, limit?)`.
5. For semantic expansion, call `keyword_research(keyword, type="related",
   project_id?, language?, location_code?, include_serp_info?, limit?)`.
6. For competitor discovery, call `domain_keywords(target, project_id?,
   language?, location_code?, limit?)`.
7. For live SERP validation, call `serp_search(query, project_id?, language?,
   location_code?)`. Prefer passing `project_id` so cached project SERPs are used
   when available.
8. Merge candidates, remove duplicates, normalize capitalization, and group by
   intent, page fit, search volume, CPC, and ranking opportunity.
9. Present the candidate list and ask the user which keywords to import.
10. Call `add_keywords(project_id, keywords=[...], sitemap_ids?)`. Without
    `sitemap_ids`, keywords are project-wide; with `sitemap_ids`, they are linked
    to the selected pages.
11. After adding keywords, confirm credit usage for metric enrichment.
12. Call `fetch_keyword_data(project_id)`.
13. Poll `get_setup_status(project_id)` every 15-30 seconds until
    `steps.keyword_data.status` is `completed` or `completed_with_failures`.
14. Call `search_keywords(project_id, query?, limit=100)` to retrieve IDs and
    enriched metrics.
15. Ask which keywords should receive full competitive analysis.
16. Confirm credit usage for `analyze_keywords`.
17. Call `analyze_keywords(project_id, keyword_ids=[...])` with 1-20 IDs.
18. Poll `get_keyword_details(keyword_id)` every 30 seconds for each analyzed
    keyword until `keyword_fit` is no longer `null`, or until the user decides to stop.
19. Use `list_keyword_serps(keyword_id, limit?, offset?)` to inspect saved SERPs.
20. Use `list_pages(project_id, search?, limit?, offset?)` when linking keywords
    to pages.
21. Call `link_keyword_to_page(project_id, keyword_id, sitemap_id)` for confirmed
    mappings.
22. Call `set_keyword_rankings(project_id, keyword_ids=[...],
    sitemap_ids=[...], is_ranking=true)` to mark which pages rank for which keywords.
23. Use `unlink_keyword_from_page(project_id, keyword_id, sitemap_id)` only after
    the user confirms removing that relationship.

## Polling Instructions

`keyword_research`, `domain_keywords`, and `serp_search` are synchronous, but they
consume credits on live calls. `fetch_keyword_data` is async and is polled through
`get_setup_status(project_id)`. `analyze_keywords` is async-like and is polled by
checking `get_keyword_details(keyword_id)` every 30 seconds until `keyword_fit` is set.

Do not poll forever. If keyword metrics or `keyword_fit` do not change after several
polls, summarize the stale state and ask whether to retry the paid step.

## User Confirmation Points

Always confirm before:

- Calling `keyword_research`, `domain_keywords`, or a live `serp_search`.
- Calling `fetch_keyword_data`.
- Calling `analyze_keywords`.
- Adding imported keywords to a project.
- Linking, unlinking, or setting ranking relationships.
- Calling `delete_keywords(project_id, keyword_ids, confirm=true)`.

For deletes, first call `delete_keywords(project_id, keyword_ids=[...],
confirm=false)` or omit `confirm` to show the preview. Only call with
`confirm=true` after explicit user approval.

## Error Handling

- `missing_keyword` or `missing_query`: Ask for a non-empty seed or SERP query.
- `missing_target`: Ask for a domain or full URL.
- `insufficient_credits`: Stop before the paid call and ask the user to add credits.
- `project_not_found`: Re-select the project or reconnect OAuth.
- `research_failed`, `lookup_failed`, or `search_failed`: Explain the provider
  failure and retry only after user approval.
- `missing_keywords` or `too_many_keywords`: Provide 1-100 keywords to `add_keywords`.
- `missing_keyword_ids` or `too_many_keywords`: Provide 1-20 IDs to `analyze_keywords`.
- `invalid_keyword_ids` or `invalid_sitemap_ids`: Refresh keywords/pages and use
  IDs from the current project.
- `missing_sitemap_ids`: Provide page IDs when `is_ranking=true`.
- `not_linked`: Treat unlink as already done and report it.
- `confirmation_required`: Ask the user before destructive delete.
- `dispatch_failed`: Stop and ask before retrying the action.
