# Contributing

Contributions are welcome. Do not submit copyrighted First Aid/AnKing source material, private faculty uploads, secure exam banks, API keys, or generated private indexes.

WIM intentionally excludes exam-question analysis. Please do not add assessment-bank ingestion or question-review features without first opening a design/security discussion.

Before opening a PR:

1. Run `pytest -q`.
2. Run `node --check app/static/app.js`.
3. Run `python -m compileall -q app`.
4. Confirm no private corpus, `.env`, database, or institution-specific files are staged.
