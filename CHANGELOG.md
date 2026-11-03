# Changelog

## 1.0.1
- Removed the instructor-facing Review depth selector.
- Instructor analyses now always use thorough semantic query expansion.
- Fast/local-only retrieval is no longer selectable from the UI or honored by public analysis endpoints.

## 1.0.0 — 2026-11-02
- Added a concise privacy page and visible v1.0.0 footer.
- Disclosed that submitted teaching material and retrieved excerpts are sent to OpenAI using the faculty member's API key.
- Made the instructor Curriculum page read-only.
- Protected course/session/material management and private material/decision listing endpoints with the admin token.
- Confirmed raw corpus/database files are not served by public routes.
- Added basic response security headers and a separate lecture-upload size ceiling.
- Hardened `.gitignore` for Anki packages, database sidecars, backups, and private curriculum seeds.
- Preserved the v0.4.9 instructor error handling, v0.4.8 report redesign, and BYO API-key flow.

## 0.4.9
- Added instructor-friendly inline error states for missing input, file problems, connection failures, and AI-provider failures.
- Added safe server-side classification for invalid API keys, billing/usage limits, rate limits, unavailable models, timeouts, and temporary provider outages without returning raw provider payloads.
- Added a concise API-key creation link and clarified that OpenAI API billing is separate from ChatGPT.
- Added a small analysis-in-progress indicator and versioned UI assets for reliable deployment.

## 0.4.8
- Removed the headline typing effect completely and added no-cache headers for all UI assets.
- Redesigned WIM reports for faster scanning: priority-grouped findings, four-count summary, stronger coverage badges, prominent recommended actions, collapsible source evidence, and cleaner secondary sections.


## 0.4.7

- Removed the one-time typing animation from the main headline.
- Restored “Find what you may have missed.” as static text.
- Bumped static asset versions so browsers load the change immediately.

## 0.4.6

- Added a one-time typing animation for the main “Find what you may have missed.” headline.
- The completed headline remains in place after the animation; it does not loop or erase.
- Respects reduced-motion preferences by showing the completed headline immediately.
- Bumped static asset versions to ensure the animation loads without stale browser cache.
- Removed the instructor-facing “Manage courses” shortcut from the analysis page; curriculum management remains available through the Curriculum page.

## 0.4.5

- Prevent stale instructor UI after deployment by disabling browser caching for HTML routes.
- Version static CSS/JavaScript URLs so layout and navigation updates load immediately.
- Retains Topics → Objectives → Slides, full-width report layout, and instructor navigation without Admin.

## 0.4.4

- Reordered the instructor workflow to Topics → Objectives → Slides.
- Removed the Admin link from instructor-facing curriculum navigation; `/admin` remains available directly.
- Simplified OpenAI connection language and status text for instructors.
- Changed the analysis workspace to a single-column layout with the WIM report below the input panel at full width.

## 0.4.3

- Made bring-your-own OpenAI API keys the default instructor flow.
- API keys are now kept only in per-tab `sessionStorage`; WIM no longer offers persistent browser storage for API keys.
- Added a visible AI connection status and a one-click **Clear key** action.
- The analysis UI now prompts for an API key before making a semantic AI request.
- Clarified that API keys are sent only with analysis requests and are not written to the WIM curriculum database.
- Added shared-deployment guidance to use HTTPS/TLS.

## 0.4.2

- Simplified the analysis homepage by removing the introductory comparison paragraph and corpus/status badges.
- Added a direct Manage courses path and a clearer empty-course state.
- Learning objectives can now be pasted directly instead of requiring a file upload.
- Removed the bottom-page disclaimer strip from the application pages.
- Added `/api/analyze/text` for topic and objective text analysis.
- Included the strict OpenAI Structured Outputs schema compatibility fix.

## 0.4.1
- Added resilient PDF ingestion: pypdf remains the primary extractor, with automatic page-level PyMuPDF fallback when pypdf hits parsing limits or page extraction errors.
- Added whole-document PyMuPDF fallback if pypdf cannot open a PDF.
- Added PyMuPDF and fontTools to runtime dependencies for complex textbook PDFs.
- Added parser regression tests for both fallback paths.

## 0.4.0

- Removed exam-question/assessment analysis from the UI, API modes, prompt layer, and material schema.
- Added backend validation that rejects unsupported material kinds.
- Added a migration that purges legacy `questions` material saved by earlier prototypes.
- Refocused WIM on three workflows: topics, slides, and learning objectives.
- Updated security and contribution guidance to make secure assessment content explicitly out of scope.

## 0.3.0

- Refocused WIM around lecture-level gap analysis rather than premature whole-curriculum auditing.
- Added Thorough and Fast retrieval modes.
- Added AI-assisted semantic query expansion for Thorough mode.
- Added source-balanced multi-query retrieval with reciprocal-rank fusion across First Aid and AnKing.
- Added explicit distinction between partial coverage and not-detected concepts.
- Added per-gap confidence and “why it matters” fields.
- Added persistent faculty decisions: Add, Already covered, Not relevant, Covered elsewhere, or Unsure.
- Added prior faculty decisions to later analysis context.
- Added browser-only decision persistence when no course is selected.
- Added faculty review summary counts and a copyable review checklist.
- Expanded tests for source balancing and faculty-decision persistence.

## 0.2.0

- Added generic multi-course curriculum storage.
- Added course descriptions as broad scope/ownership context.
- Added sessions/lectures under courses.
- Added optional persistence of topics, slides, objectives, and assessment material.
- Added curriculum manager UI.
- Added private curriculum JSON import.
- Added scope-aware WIM output and likely-owner-course field.
- Added explicit independent/non-affiliation language.
- Kept institution-specific curriculum content outside the public repo.
- Expanded automated tests.

## 0.1.0

- Initial WIM workflows: topics, slides, objectives, and questions.
- Private First Aid PDF ingestion.
- Private AnKing APKG ingestion.
- Local SQLite FTS5 retrieval.
- Bring-your-own OpenAI API key.
