---
name: rankm8-project-setup
description: Use this skill when a user wants to create a new RankM8 SEO project and run the complete headless setup pipeline through the RankM8 MCP server.
---

# RankM8 Project Setup

## Purpose

Guide an AI client through a complete RankM8 project setup without opening the web UI.
The playbook creates the project, discovers pages, crawls selected pages, enriches
keywords, runs keyword analysis, and stores goals plus competitors.

Use the MCP server as the source of truth. Keep the `project_id` returned by
`create_project` and pass it to every later project-scoped call.

## Required MCP Tools

Setup tools:

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

Pipeline handoff tool:

- `analyze_keywords(project_id, keyword_ids)`

## Step-by-Step Workflow

1. Ask for the target website URL if it is missing.
2. Confirm credit usage, then call `analyze_website(url)` to infer `company_name`,
   `company_location`, `company_country`, and `language`.
3. Show the inferred project data and ask the user to confirm or correct it before
   project creation.
4. Call `create_project(name, project_url, company_name, company_location,
   company_country, language, project_scope, coordinates?)`.
5. Store the returned `project_id` for every later call.
6. Confirm credit usage for page discovery, then call
   `scrape_sitemap(project_id, sitemap_url?)`.
7. Poll `get_setup_status(project_id)` every 15-30 seconds until
   `steps.sitemap_scrape.status` is `completed` or `completed_with_failures`.
8. If `steps.sitemap_scrape.status` is `failed`, if `pages_found` is `0`, or if
    the response suggests a fallback, ask the user before the paid fallback and
    call `serp_site_search(project_id)`.
9. Call `suggest_selection(project_id, type="pages", limit=10)`.
10. Show page suggestions, URLs, and reasoning; ask the user to approve or change
    the final `sitemap_ids` list, ideally 5-10 pages and never more than 25.
11. Confirm credit usage, then call `crawl_pages(project_id, sitemap_ids=[...])`.
12. Poll `get_setup_status(project_id)` every 15-30 seconds until
    `steps.keyword_extraction.status` is `completed` or `completed_with_failures`.
13. Confirm credit usage, then call `fetch_keyword_data(project_id)`.
14. Poll `get_setup_status(project_id)` every 15-30 seconds until
    `steps.keyword_data.status` is `completed` or `completed_with_failures`.
15. Call `suggest_selection(project_id, type="keywords", limit=10,
    sitemap_ids=[...])` when page-scoped suggestions are useful.
16. Show keyword suggestions, metrics, and reasoning; ask the user to confirm up
    to 10 `keyword_ids`.
17. Call `update_project(project_id, selected_keyword_ids=[...])` before keyword
    analysis so setup status is scoped to the confirmed keywords.
18. Confirm credit usage, then call `analyze_keywords(project_id, keyword_ids=[...])`.
19. Poll `get_setup_status(project_id)` every 15-30 seconds until
    `steps.keyword_analysis.status` is `completed` or `completed_with_failures`.
20. Call `list_competitor_candidates(project_id, keyword_ids=[...],
    max_position=10, limit=10)`.
21. Show candidates and ask the user to select up to 3-5 competitor domains.
22. Ask for up to 3 business goals from `more_traffic`, `more_leads`,
    `higher_sales`, `brand_awareness`, `local_visibility`.
23. Call `update_project(project_id, business_goals=[...],
    selected_competitors=[...], selected_sitemap_ids=[...],
    selected_keyword_ids=[...])`.

## Polling Instructions

Treat `get_setup_status(project_id)` as the only setup polling tool. Watch
`overall_status`, `current_step`, `steps`, `poll_again_in_seconds`, `next_action`,
and `warning`.

Valid step statuses are `not_started`, `pending`, `running`, `completed`,
`completed_with_failures`, and `failed`.

Never poll forever. If a response contains `warning`, explain it to the user and
either retry the indicated step tool, switch to a documented fallback, or stop.
If any step is `failed`, stop the pipeline and handle the error before continuing.

## User Confirmation Points

Always get explicit user confirmation before:

- Calling any credit-consuming tool: `analyze_website`, `scrape_sitemap`,
  `serp_site_search`, `crawl_pages`, `fetch_keyword_data`, `analyze_keywords`.
- Calling `create_project` with final project data.
- Crawling selected `sitemap_ids`.
- Analyzing selected `keyword_ids`.
- Saving `selected_competitors` and `business_goals`.

GSC, GMB, and WordPress connections cannot be completed through MCP. Refer the user
to project settings in the RankM8 web UI for those browser OAuth flows.

## Error Handling

- `project_limit_reached`: Tell the user their plan limit is reached and suggest a
  plan upgrade or deleting unused projects.
- `insufficient_credits`: Stop before the paid step and ask the user to add credits.
- `invalid_parameter`: Show the invalid field and request corrected input.
- `invalid_url` or `missing_url`: Ask for a public HTTP(S) website URL.
- `invalid_sitemap_ids` or `invalid_keyword_ids`: Re-list pages or keywords and ask
  the user to choose IDs from the current project.
- `missing_sitemap_ids` or `too_many_pages`: Ask for 1-25 page IDs.
- `analysis_failed`, `search_failed`, or `dispatch_failed`: Stop, summarize the
  failed step, and retry only after the user approves.
- `project_not_found`: Re-run project selection or ask the user to reconnect OAuth.
