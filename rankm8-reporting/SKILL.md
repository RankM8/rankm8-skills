---
name: rankm8-reporting
description: Use this skill when a user wants a monthly SEO performance report from RankM8 projects, Search Console, Google Business Profile, and tracked keywords.
---

# RankM8 Monthly Reporting

## Purpose

Create a concise monthly SEO report by combining Search Console performance,
Google Business Profile insights for local projects, and RankM8 tracked keyword
data. Focus on business outcomes, changes from the previous period, winners,
losses, and recommended next actions.

## Required MCP Tools

Reporting tools:

- `get_gsc_performance(project_id, dimension?, url?, query?, start_date?, end_date?, date_range?, compare_with_previous?, limit?, sort_by?)`
- `get_gmb_data(project_id, date_range?, include_keywords?, keyword_limit?)`
- `search_keywords(project_id, query?, limit?)`

Useful supporting tools:

- `list_projects(limit?, offset?)`
- `get_project(project_id)`
- `list_pages(project_id, search?, limit?, offset?)`
- `list_project_audit_issues(project_id)`

Optional drill-down tools:

- `get_keyword_details(keyword_id)`
- `list_keyword_serps(keyword_id, limit?, offset?)`
- `get_gsc_performance(project_id, dimension="query", query?, start_date?, end_date?, date_range?, compare_with_previous?, limit?, sort_by?)`
- `get_gsc_performance(project_id, dimension="page", url?, start_date?, end_date?, date_range?, compare_with_previous?, limit?, sort_by?)`
- `get_gsc_performance(project_id, dimension="date", start_date?, end_date?, date_range?, compare_with_previous?, limit?, sort_by?)`

## Step-by-Step Workflow

1. Identify and confirm the reporting project. Use `list_projects()` if
   `project_id` is unknown.
2. Ask for the reporting month if it is not explicit. For a monthly report,
   prefer exact `start_date` and `end_date` in `YYYY-MM-DD`.
3. Call `get_project(project_id)` to understand project URL, scope, and status.
4. Call `get_gsc_performance(project_id, start_date, end_date,
   compare_with_previous=true, limit=25, sort_by="clicks")` for the executive
   summary.
5. If GSC is connected, call `get_gsc_performance(project_id, dimension="query",
   start_date, end_date, compare_with_previous=true, limit=25,
   sort_by="clicks")` for top queries.
6. Call `get_gsc_performance(project_id, dimension="query", start_date,
   end_date, limit=25, sort_by="impressions")` to find high-impression
   opportunities.
7. Call `get_gsc_performance(project_id, dimension="page", start_date,
   end_date, compare_with_previous=true, limit=25, sort_by="clicks")` for top
   landing pages.
8. Call `get_gsc_performance(project_id, dimension="date", start_date,
   end_date, limit=100, sort_by="clicks")` for trend context.
9. Call `search_keywords(project_id, query?, limit=100)` to summarize tracked
   keyword volume, fit, intent, and ranking status.
10. For local projects, call `get_gmb_data(project_id, date_range="28d",
    include_keywords=true, keyword_limit=20)`. If the month is longer or shorter,
    state that GMB insight rows are based on the available 30-day database summary.
11. Optionally call `list_project_audit_issues(project_id)` to add a technical SEO
    risk snapshot.
12. Build the report with sections: Executive Summary, GSC Performance, Query
    Winners and Opportunities, Landing Pages, Keyword Portfolio, Local Performance,
    Technical Risks, Recommended Actions, and Data Caveats.
13. Include period-over-period changes when `comparison.changes` is available.
    Remember that negative change for average position means improvement.
14. If `connected=false` for GSC or GMB, include a data caveat instead of treating
    missing data as zero performance.
15. Prioritize recommendations by estimated impact and effort, and connect each
    recommendation to the observed metric.

## Polling Instructions

The reporting tools in this playbook are synchronous. Do not poll
`get_gsc_performance`, `get_gmb_data`, or `search_keywords`.

If the user asks you to run fresh keyword analysis as part of the report, switch to
the Keyword Research playbook: confirm credit usage, call
`analyze_keywords(project_id, keyword_ids)`, and poll `get_keyword_details(keyword_id)`
every 30 seconds until `keyword_fit` is available. Do not hide this paid detour
inside a normal monthly report.

## User Confirmation Points

Always confirm before:

- Selecting the project when multiple projects match.
- Using a custom date range if the user's wording is ambiguous.
- Running optional paid analysis outside the reporting tools.
- Sharing or exporting the report outside the current AI client.
- Marking issues or tasks as resolved in any external system.

There are no delete tools in this reporting workflow. If a user asks to delete
keywords or pages while reviewing the report, switch to the relevant maintenance
playbook and require explicit destructive confirmation.

## Error Handling

- `project_not_found`: Re-run project selection or ask the user to reconnect OAuth.
- `invalid_dimension`: Use only `query`, `page`, or `date`.
- `connected=false` from `get_gsc_performance`: Report that Google Search Console
  is not connected and omit GSC claims.
- `connected=false` with `notRelevant=true` from `get_gmb_data`: State that Google
  Business Profile reporting is only relevant for local projects.
- `connected=false` from `get_gmb_data`: Report that Google Business Profile is not
  connected and omit local insights.
- Empty keyword results from `search_keywords`: State that no tracked keywords
  were available for the project.
- Invalid `date_range`: Use `7d`, `28d`, or `90d`, or pass exact `start_date` and
  `end_date` for monthly reports.
