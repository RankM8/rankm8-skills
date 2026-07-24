---
name: rankm8-content-pipeline
description: Use this skill when a user wants to create SEO analysis plans, outlines, full content, meta information, or AI images with RankM8 MCP generation tools.
---

# RankM8 Content Generation

## Purpose

Create SEO content from RankM8 project data in a controlled sequence:
analysis plan, outline, content, meta information, and optional image generation.
Use real pages, keywords, SERPs, and competitor context whenever possible.

## Required MCP Tools

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

## Step-by-Step Workflow

1. Identify and confirm `project_id`. Never auto-select a project for generation.
2. Ask the user for the topic, target page, target keywords, desired length, and
   tone if they were not provided.
3. Use `list_pages(project_id, search?, limit?, offset?)` to find target pages
   and confirm `page_ids`.
4. Use `search_keywords(project_id, query?, limit?)` to find target keywords and
   confirm `keyword_ids`.
5. For each target keyword, check `keyword_fit`. If it is `null`, ask whether to
   improve data quality before writing.
6. If the user approves, run `analyze_keywords(project_id, keyword_ids=[...])` and
   poll `get_keyword_details(keyword_id)` every 30 seconds until `keyword_fit` is
   no longer `null` or the user asks to continue without full analysis.
7. Optionally call `list_models(category="chat")` when the user asks for a model
   or quality/cost tradeoff.
8. Confirm credit usage for `generate_analyse_plan`.
9. Call `generate_analyse_plan(project_id, prompt?, model?, page_ids?,
   keyword_ids?, settings?)`.
10. Poll `get_generation_status(entity_type="plan", entity_id=writing_plan_id)`
    every 30 seconds until `completed` or `failed`.
11. Review the completed plan with the user before creating an outline.
12. Confirm credit usage for `generate_outline`.
13. Call `generate_outline(project_id, prompt?, model?, page_ids?, keyword_ids?,
    plan_id=writing_plan_id, settings?)`.
14. Poll `get_generation_status(entity_type="outline",
    entity_id=writing_outline_id)` every 30 seconds until `completed` or `failed`.
15. Review the outline with the user and ask for approval before full content.
16. Confirm credit usage for `generate_content`.
17. Call `generate_content(project_id, prompt?, model?, page_ids?, keyword_ids?,
    plan_id=writing_plan_id, outline_id=writing_outline_id, settings?)`.
18. Poll `get_generation_status(entity_type="content",
    entity_id=writing_content_id)` every 30 seconds until `completed` or `failed`.
19. Confirm credit usage for `generate_meta_info`.
20. Call `generate_meta_info(project_id, content_id=writing_content_id, prompt?,
    model?, page_ids?, keyword_ids?)`. This is synchronous; no polling is needed.
21. If an image is requested, ask for image direction, references, and final
    project confirmation.
22. Confirm credit usage for `generate_image`.
23. Call `generate_image(prompt, project_id, reference_image_base64?, reference_image_urls?, reference_image_ids?, model?)`.

## Polling Instructions

For generation jobs, always use `get_generation_status(entity_type, entity_id)`.
Use `entity_type="plan"` for `writing_plan_id`, `entity_type="outline"` for
`writing_outline_id`, and `entity_type="content"` for `writing_content_id`.

Valid generation statuses are `pending`, `processing`, `completed`, and `failed`.
Poll every 30 seconds until terminal. If a response contains `poll_again_in_seconds`,
follow it. If a response contains `warning` or times out to `failed` with
`failure_reason="timeout"`, stop and ask before starting a new job.

`generate_full_content` is an alternative single-call pipeline. It returns
`writing_plan_id`, `writing_outline_id`, and `writing_content_id`; poll the content
entity for the whole pipeline and optionally poll plan/outline for intermediate
artifacts.

## User Confirmation Points

Always confirm before:

- Calling any credit-consuming generation tool.
- Using a selected project for generated assets or content.
- Running `analyze_keywords` to improve keyword quality.
- Continuing from plan to outline and from outline to content.
- Using reference images with `generate_image`.

No delete tools are part of this workflow. If the user asks to remove generated
content from RankM8, explain that this playbook does not expose a delete step.

## Error Handling

- `insufficient_credits`: Stop and ask the user to add credits or choose a cheaper step.
- `invalid_model`: Call `list_models(category="chat")` for text or
  `list_models(category="image-generation")` for images.
- `project_not_found`: Re-select the project or reconnect OAuth.
- `plan_not_found`, `outline_not_found`, or `content_not_found`: Re-poll or use IDs
  from the current project only.
- `invalid_settings`: Correct `settings` values like `content_length`,
  `custom_word_count`, `writing_style`, `topical_terms_optimization`,
  `analysis_depth`, or `section_depth`.
- `missing_prompt`: Provide a prompt for `generate_image`.
- `generation_failed` or `reference_image_failed`: Show the error details and retry
  only after the user confirms changed inputs.
- `not_found`: Treat as access denied or stale ID; re-select the artifact.
