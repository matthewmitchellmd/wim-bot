# WIM Bot — “What’d I Miss?”

WIM Bot is an independent open-source curriculum support tool for medical educators. Its primary job is simple:

> Give WIM a lecture topic list, slide deck, or learning objectives and ask what Step 1-relevant material may be missing or only partially covered.

WIM compares faculty material against a **private, user-supplied reference corpus**. The reference design uses **First Aid for the USMLE Step 1 + AnKing**. Those materials are **not included in this repository**.

> WIM Bot is not affiliated with, endorsed by, or sponsored by any medical school, USMLE, First Aid, AnKing, or other educational resource provider.

## What v1.0.1 includes

v1.0.1 prioritizes **lecture-level review** over whole-curriculum auditing.

- **Check my topics** — paste the concepts planned for a lecture.
- **Check my objectives** — paste lecture learning objectives directly into WIM.
- **Check my slides** — upload PPTX/PDF/DOCX/TXT/MD/CSV.
- Distinguishes **not detected** from **partial coverage**.
- Separates **priority** from **confidence**.
- Uses source-balanced retrieval so a large AnKing corpus does not crowd out First Aid evidence.
- Uses **thorough semantic retrieval** for every instructor analysis: the faculty member's model generates a small set of related search queries before local retrieval.
- Shows First Aid/AnKing evidence for each suggested gap.
- Lets faculty disposition each suggestion as:
  - Add / address
  - Already covered
  - Not relevant
  - Covered elsewhere
- Persists faculty decisions when a course/session is selected and feeds those decisions back into later analyses.
- Generates a copyable faculty review checklist.

The existing multi-course/session structure remains available as optional context and storage, but WIM does **not** claim to perform a complete whole-curriculum Step 1 audit from sparse course descriptions.

## Design principle

WIM should not behave like this:

> “AnKing has a card about X, therefore you must teach X.”

It should behave like this:

> “X appears relevant to the material you submitted, was not detected (or was only partially covered), and is supported by these retrieved First Aid/AnKing references. Here is why it may matter. You decide whether to add it.”

## Public code vs private data

```text
PUBLIC REPOSITORY
├── app/
├── tests/
├── Dockerfile
├── README.md
└── application logic

PRIVATE DEPLOYMENT DATA
├── First Aid index
├── AnKing index
├── optional course/session descriptions
├── saved faculty material
├── faculty review decisions
└── secrets
```

The repository ignores:

```text
private_corpus/*
private_config/*
data/*.db
.env
uploads/
```

The included ingestion routes parse uploaded First Aid/AnKing files into a private SQLite index and do not save the original source files. Public instructor pages can read course/session scope, but course/session/material management endpoints require the admin token. Saved material contents and faculty decision history are not exposed by public listing endpoints.

Normal lecture uploads are not intentionally retained as source files. If a faculty member explicitly selects **Save this material**, WIM stores the extracted text in the private database. During an analysis, submitted teaching material plus selected reference excerpts are sent to OpenAI using the faculty member's API key.

Keeping copyrighted content out of GitHub prevents redistribution through this repository; it does **not** establish permission to ingest/process the content. Verify the licenses and permissions that apply to any reference source or institutional material you use.

## Retrieval

WIM uses thorough semantic query expansion for every instructor analysis:

```text
faculty material
      ↓
AI retrieval-plan call
(topic labels + synonym-aware queries)
      ↓
local First Aid search  ─┐
                         ├─ reciprocal-rank fusion
local AnKing search     ─┘
      ↓
source-balanced evidence set
      ↓
AI coverage comparison
      ↓
WIM report
```

This normally uses **two model calls** per analysis: one small retrieval-planning call and one final analysis call. The model is supplied by the faculty member through their own API key.

If the retrieval-planning call fails, WIM falls back to direct local retrieval and still attempts the final analysis. There is no instructor-facing fast mode.

## Faculty decisions

When a course is selected, review decisions are stored in the private WIM database. A session-specific decision applies to that session; course-level decisions can provide broader context.

On later analyses, prior decisions are included in the model context. WIM is instructed not to repeatedly recommend concepts already marked **Not relevant** or **Covered elsewhere** unless the new material changes the reason.

If no course is selected, decisions are stored only in the current browser using local storage.

## Optional course/session context

Courses and sessions are **not required** to run WIM. They are useful when you want to:

- give WIM a broad scope boundary;
- save lecture topics, slides, and/or learning objectives;
- remember faculty decisions for that lecture;
- gradually build a curriculum map over time.

Course descriptions are treated as broad context, **not as proof** that a concept is actually taught.

## Private curriculum seed format

WIM can import a private JSON file from `/admin`:

```json
{
  "courses": [
    {
      "name": "Course name",
      "description": "Broad scope description",
      "sequence": 1,
      "duration_weeks": 6
    }
  ]
}
```

Do not commit institution-specific seeds if you want the public repository to remain generic.

## AI usage

WIM uses a **bring-your-own OpenAI API key** adapter in this release. Each faculty member enters their own key through **Connect OpenAI**. The browser keeps the key in `sessionStorage` for that tab only; WIM does not place API keys in `localStorage`, the curriculum database, or source-controlled configuration. The key is sent to the WIM server in an `X-OpenAI-API-Key` request header only when an analysis is run, and the server uses it for the OpenAI request without persisting it.

Closing the browser tab clears the session-stored key. Shared deployments should use HTTPS/TLS so the key and uploaded teaching material are encrypted in transit.

The default model is configurable with `WIM_DEFAULT_MODEL`; the UI also lets each user choose a model. The AI adapter is isolated from curriculum storage so other providers or account-auth flows can be added later.

The browser UI asks for an API key before running a semantic analysis. The backend still retains a retrieval-only no-key fallback for API clients and local diagnostics.

## Setup

```bash
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env
```

Set a strong admin token:

```bash
export WIM_ADMIN_TOKEN='choose-a-long-random-token'
export WIM_DB_PATH='data/wim.db'
export WIM_MAX_ANALYSIS_UPLOAD_MB='60'
```

Run:

```bash
uvicorn app.main:app --reload
```

Then open:

- App: `http://127.0.0.1:8000`
- Curriculum: `http://127.0.0.1:8000/curriculum`
- Private admin: `http://127.0.0.1:8000/admin`
- Privacy: `http://127.0.0.1:8000/privacy`

## Secure assessment content

WIM intentionally does **not** accept or analyze exam questions in this release. This is a product-level security boundary, not merely a hidden UI option: question analysis modes are absent from the API schema and upload routes, and legacy `questions` material from earlier prototypes is removed from the private database when v0.4 opens it.

Do not upload secure exam banks or other restricted assessment content to WIM.

## Private corpus ingestion

### First Aid

```bash
curl -X POST http://127.0.0.1:8000/api/admin/ingest/first-aid \
  -H "X-WIM-Admin-Token: $WIM_ADMIN_TOKEN" \
  -F "file=@/private/path/FirstAid.pdf"
```

### AnKing

```bash
curl -X POST http://127.0.0.1:8000/api/admin/ingest/anking \
  -H "X-WIM-Admin-Token: $WIM_ADMIN_TOKEN" \
  -F "file=@/private/path/AnKing.apkg"
```

## Testing

```bash
pytest -q
node --check app/static/app.js
python -m compileall -q app
```

## Important limitations

- WIM is a curriculum-support tool, not an authoritative definition of what must be taught for Step 1.
- Retrieval quality depends on how well the private First Aid and AnKing sources were parsed/indexed.
- AnKing card density is not treated as a direct importance score.
- Course descriptions cannot establish that a concept is actually taught elsewhere.
- Faculty review remains required.

## License

MIT. Reference corpus and private curriculum content are not included and remain subject to their own licenses/permissions.
