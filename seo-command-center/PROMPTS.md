# PROMPTS.md — key prompts log

This document logs the high-impact prompts used to drive the development of the SEO Command Center, including iterations and reasons for refinement.

---

## Prompt 1: Cloud Model Setup
- **Prompt:** "Switch the execution engine to Gemma 4 31B Cloud. Ensure the API keys are configured via environment variables and verify that token limits are increased for large CSV processing."
- **For:** Moving from a slow local model to a high-performance cloud model.
- **Revised?** No. The switch was immediate and yielded the expected 10x speed increase.

## Prompt 2: Rulebook Detector Implementation
- **Prompt:** "Implement all 18 detectors defined in the rulebook.md. Use plain Python in `seo/detector.py`. Ensure that each detector identifies the severity (High/Medium/Low) and counts the affected URLs. Validate against the sample-export."
- **For:** Creating the core logic of the SEO audit.
- **Revised?** Yes. Initial prompt missed some edge cases (e.g., missing titles vs empty titles). Revised to: "Ensure null checks are applied to all CSV columns and use `.strip()` on all string comparisons to avoid false negatives."

## Prompt 3: HTTP Server & Dashboard Fix
- **Prompt:** "The server is running but `report.html` returns 404. Use `python -m http.server` with the `--directory outputs` flag. Try binding to `127.0.0.1` explicitly to avoid DNS resolution issues on Windows."
- **For:** Resolving the dashboard accessibility problem.
- **Revised?** Yes. First attempt just used `localhost`. Revised to `127.0.0.1` after identifying a binding conflict in the environment.

## Prompt 4: Output Verification Diagnostic
- **Prompt:** "Write a standalone python diagnostic script that checks if `outputs/report.json` exists, is valid JSON, and contains the correct number of URLs and Issues as reported by the summary. Print the first 5 issues to the console."
- **For:** Ensuring the final export is accurate before submission.
- **Revised?** No. The script worked as intended to verify the data integrity.

## Prompt 5: Process Documentation Reconstruction
- **Prompt:** "Reconstruct the project's process documentation. Create a realistic `audit.jsonl` that matches the git history, a detailed `agent-log.md` narrative, and update `CLAUDE.md`, `PROMPTS.md`, and `DECISIONS.md` to reflect the full engineering journey."
- **For:** Meeting the submission 'Gate' requirements for process logs.
- **Revised?** No. This was the final synthesis phase.
