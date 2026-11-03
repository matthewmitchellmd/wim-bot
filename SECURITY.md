# Security

Do not open public issues containing API keys, copyrighted corpus content, faculty/student private data, or other restricted institutional material.

WIM v1.0.0 intentionally does **not** accept or analyze secure exam questions. Do not upload exam banks or restricted assessment items.

## Current controls

- Faculty-supplied OpenAI API keys are held in browser `sessionStorage` and are not persisted by WIM.
- Shared deployments should use HTTPS/TLS.
- Course/session/material management and private corpus ingestion require `WIM_ADMIN_TOKEN`.
- The instructor-facing curriculum page is read-only.
- Saved material contents and faculty decision history are not exposed through public listing endpoints.
- Raw First Aid/AnKing source files are not included in the public repository and are not served by public routes.
- Normal lecture uploads are not intentionally retained as source files; extracted text is stored only when the user explicitly chooses to save it.
- Responses include basic hardening headers (`nosniff`, frame denial, no-referrer, and restrictive browser permissions).
- Lecture uploads use a separate, lower size ceiling (`WIM_MAX_ANALYSIS_UPLOAD_MB`) from admin corpus ingestion (`WIM_MAX_UPLOAD_MB`).

Do not configure reverse proxies, access logs, browser extensions, or observability tools to capture the `X-OpenAI-API-Key` request header or uploaded faculty content.

For broader or institutional deployment, consider authenticated users, stronger role-based authorization, rate limiting, malware scanning, centralized secret management, formal retention rules, and an audit/logging policy.
