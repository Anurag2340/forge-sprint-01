# CLAUDE.md — project memory for the SEO Command Center build

## What we are building
A Claude Code plugin that ingests a Screaming Frog SEO export (`internal_all.csv` + issue CSVs), audits it against the rulebook, prioritizes issues, writes fixes, serves a live dashboard at localhost:7700, and outputs `outputs/report.json` + `outputs/report.html`.

## Goal & Constraints
- **Goal**: Provide a high-accuracy, automated SEO audit that correctly categorizes 18 rulebook issues by severity.
- **Hard Rules**:
    - Deterministic detection in **plain Python** (csv/pandas).
    - Use LLMs only for judgment (rewriting titles/metas, choosing redirect targets).
    - `outputs/report.json` MUST strictly follow `report.schema.json`.
    - Filter to `text/html` + indexable pages before performing title/meta checks.
    - No hard-coding to the sample export; must handle unseen crawls.
    - Minimize model calls (one page per fix).

## Architecture
The system follows a linear pipeline:
1. **`seo_load`**: Ingests Screaming Frog CSVs $\rightarrow$ `RUN["rows"]`.
2. **`seo_detect`**: Runs 18 deterministic checkers in `seo/detector.py` $\rightarrow$ `RUN["issues"]`.
3. **`seo_fix`**: Generates AI-driven fixes for titles and redirects $\rightarrow$ `RUN["fixes"]`.
4. **`seo_recommend`**: Prioritizes issues into actionable recommendations $\rightarrow$ `RUN["recommendations"]`.
5. **`seo_report`**: Exports the current state to `outputs/report.json`.
6. **`seo_export`**: Renders the JSON state into a styled `outputs/report.html`.
7. **Dashboard**: `mcp/server.py` hosts a live cockpit at localhost:7700 via SSE.

## Key Decisions
- **Cloud Model Transition**: Switched from local Qwen to Gemma 4 31B Cloud for a 10x performance boost (34s vs 7m).
- **Modular Detectors**: Built a library of independent checker functions for better maintainability and rulebook coverage.
- **Binding Fix**: Forced `127.0.0.1` binding for the HTTP server to resolve Windows-specific DNS/404 issues.

## Learnings & Gotchas
- **Token Limits**: Local models struggle with large CSVs; cloud models are essential for "full-site" audits.
- **Path Resolution**: In Windows, relative paths for the HTTP server `--directory` can be tricky; always verify the CWD.
- **Data Integrity**: Empty cells in CSVs can cause `TypeError` in Python; explicit string casting and null checks are required.
- **Process Logs**: `audit.jsonl` and `agent-log.md` are critical for grading; they must be meticulously maintained to avoid a score cap.

## Conventions
- Commit after each working step with a real message.
- Run `python run.py sample-export/` to test end to end.
- Validate `report.json` using the provided schema before finalizing.
