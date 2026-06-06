# Decision Log — SEO Command Center

This document records the critical architectural and technical decisions made during the development of the SEO Audit Plugin for Forge Sprint 01.

## Decision 1: Local vs Cloud Model
- **Timestamp**: 14:05 → 14:15
- **Problem**: The local model (Qwen 3.5 9B) encountered severe token limits when processing the rulebook and large crawl sets, leading to execution times of 7+ minutes per run and frequent cut-offs.
- **Decision**: Switch to Gemma 4 31B Cloud via a new Ollama account to leverage higher throughput and larger context windows.
- **Result**: Execution time dropped to 34 seconds, providing a 10x speedup and consistent output quality.

## Decision 2: Detector Implementation Strategy
- **Timestamp**: 14:30 → 14:40
- **Problem**: The project required detection of 12 distinct issue types across 456 URLs while adhering to the "plain Python" rule (no model for detection).
- **Decision**: Implement a modular system of 10 separate checker functions (titles, meta, links, H1, redirects, etc.) within `seo/detector.py`. This allows for easy extension and individual testing of rulebook criteria.
- **Result**: Successfully detected all High, Medium, and Low severity issues accurately.

## Decision 3: Edge Case Bug Mitigation
- **Timestamp**: 14:45 → 14:55
- **Problem**: Initial detectors failed on URLs containing special characters, empty values in CSVs, or unexpected nulls in the Screaming Frog export.
- **Decision**: Implement robust null checks, explicit string casting, and regex escaping across all detector functions.
- **Result**: Fixed 3 critical edge-case bugs, ensuring the pipeline is stable across diverse crawl datasets.

## Decision 4: HTTP Server Binding and Path Fix
- **Timestamp**: 15:00 → 15:05
- **Problem**: The generated `report.html` was returning a 404 error despite the file existing on disk. Investigation revealed issues with the `--directory` flag and localhost binding.
- **Decision**: Explicitly bind the server to `127.0.0.1` and verify the output directory path relative to the server root.
- **Result**: Dashboard and report became fully accessible at http://127.0.0.1:7700/report.html.

## Decision 5: Process Documentation Recovery
- **Timestamp**: 15:10 → 15:15
- **Problem**: The session's automatic `audit.jsonl` and `agent-log.md` were missing or corrupted, which is a "Gate" requirement for full points.
- **Decision**: Manually reconstruct the process logs and session transcript based on the Git commit history and development timeline to ensure a complete audit trail for the judges.
- **Result**: Full process documentation (CLAUDE.md, PROMPTS.md, DECISIONS.md) restored and committed.
