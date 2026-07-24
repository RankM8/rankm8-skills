---
name: rankm8-site-audit
description: Use this skill when a user wants to run, inspect, triage, or update technical SEO audits for RankM8 project pages via MCP.
---

# RankM8 Site Audit

## Purpose

Run a technical SEO audit for one RankM8 page, poll the asynchronous job, inspect
the latest results, triage issues, guide fixes, and update issue or check workflow
status when the user confirms the work is done.

An audit runs per page, not per whole website. Use `sitemap_id` for the selected
page and `audit_id` for the returned audit result.

## Required MCP Tools

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

Useful selectors:

- `list_projects(limit?, offset?)`
- `list_pages(project_id, search?, limit?, offset?)`

## Step-by-Step Workflow

1. Identify the project with `list_projects()` if `project_id` is unknown.
2. Identify the page with `list_pages(project_id, search?, limit?, offset?)` if
   `sitemap_id` is unknown.
3. Confirm the exact project and page with the user before running the audit.
4. Explain that `trigger_site_audit` consumes credits and ask for explicit approval.
5. Call `trigger_site_audit(project_id, sitemap_id)`.
6. Store any returned `job_id` and the selected `sitemap_id`.
7. Poll `get_site_audit_status(project_id, sitemap_id)` every 15-30 seconds.
8. Continue polling until the latest job is terminal. Terminal job statuses are
   `completed`, `failed`, `cancelled`, and `insufficient_tokens`.
9. Stop on `failed`, `cancelled`, or `insufficient_tokens`; summarize the state and
   ask the user before retrying.
10. When completed, call `get_site_audit(project_id, sitemap_id)` to retrieve the
    latest score, meta data, Core Web Vitals, resources, and summary.
11. Store the returned `audit_id` for all detail calls.
12. Call `list_audit_issues(project_id, audit_id, limit=50, offset=0)` and page
    through more results if `total` exceeds the first page.
13. Optionally call `get_audit_checks(project_id, audit_id, status?, severity?)`
    for check-level evidence.
14. Optionally call `get_audit_images(project_id, audit_id, status?, limit?, offset?)` for
    image problems.
15. Optionally call `get_audit_headings(project_id, audit_id, level?, limit?, offset?)` for heading
    structure problems.
16. Group findings by severity, impact, and fix owner. Do not mark anything
    resolved merely because it was discussed.
17. Give the user concrete remediation instructions or, if code/content edits are
    in scope, perform the edits using the client's normal file or website tools.
18. After the user confirms a fix was completed, call
    `update_audit_issue_status(project_id, issue_id, status="resolved")` or
    `update_check_status(project_id, check_id, status="resolved")`.
19. Use `status="ignored"` only when the user explicitly accepts the issue as
    intentional or irrelevant.
20. Call `list_project_audit_issues(project_id)` at the end to summarize remaining
    project-wide issues.

## Polling Instructions

Use only `get_site_audit_status(project_id, sitemap_id)` for live audit progress.
The `SiteAudit` entity may not exist until the import finishes, so do not poll
`get_site_audit` for progress.

Poll every 15-30 seconds, or follow a returned interval if present. Watch
`job.is_terminal`, `job.status`, progress fields, latest audit summary, and
`warning`. A warning after long runtime means the job may be stale; stop and ask
before triggering another paid audit.

## User Confirmation Points

Always get explicit user confirmation before:

- Selecting a project when multiple projects match.
- Selecting the audited page when multiple pages match.
- Calling `trigger_site_audit`, because it consumes credits.
- Retrying a failed or stale audit, because it may create another DataForSEO task.
- Changing issue/check status to `resolved` or `ignored`.

There is no MCP delete operation in this audit workflow. Do not invent one.

## Error Handling

- `audit_already_running`: Do not trigger again; continue polling the existing job.
- `insufficient_credits`: Stop and ask the user to add credits before retrying.
- `audit_not_found`: Check whether the audit completed; otherwise trigger an audit
  only after confirmation.
- `issue_not_found` or `check_not_found`: Refresh issues/checks and use IDs from
  the current project.
- `invalid_status`: Use only `open`, `in_progress`, `resolved`, or `ignored`.
- `invalid_sitemap_ids`: Re-run `list_pages` and choose a valid page ID.
- `missing_audit_reference`: Call `get_site_audit(project_id, sitemap_id)` or pass
  a valid `audit_id`.
- `dispatch_failed`: Stop and ask before retrying the trigger.
- `project_not_found`: Re-select the project or ask the user to reconnect OAuth.
