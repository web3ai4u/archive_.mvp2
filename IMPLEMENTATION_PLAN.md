# IMPLEMENTATION PLAN
# Архив Терпения — MVP

## Version

0.1

## Status

IMPLEMENTATION

## Goal

Создать минимально работающий Telegram-продукт,
который принимает текстовый материал,
анализирует его через LLM,
сохраняет результат в SQLite
и возвращает пользователю структурированный результат.

---

# 1. Product Definition

## Что пользователь делает

Пользователь отправляет Telegram-боту материал.

Например:

"Сервис превращает старые документы компании
в инструкции для новых сотрудников."

## Что делает система

1. Принимает материал.
2. Сохраняет оригинал.
3. Нормализует.
4. Анализирует.
5. Проверяет качество.
6. Анализирует коммерческую ценность.
7. Сохраняет результат.
8. Возвращает результат пользователю.

---

# 2. MVP Boundary

## Входит

- Telegram;
- текст;
- OpenRouter;
- SQLite;
- structured analysis;
- quality gate;
- monetization analysis;
- logging;
- tests.

## Не входит

- image processing;
- PDF;
- RAG;
- vector DB;
- multi-agent;
- web UI;
- RSS;
- automatic publishing;
- Genebu integration;
- payments.

Они добавляются только после успешного MVP.

---

# 3. Infrastructure

Target:

2 CPU
2 GB RAM
32 GB SSD
Linux
Python 3.x
venv

No GPU.

---

# 4. Architecture

Telegram
    |
    v
bot.py
    |
    v
pipeline.py
    |
    +-------> providers.py
    |              |
    |              v
    |         OpenRouter
    |
    +-------> db.py
                   |
                   v
                 SQLite

---

# 5. Components

## bot.py

Responsibilities:

- Telegram initialization;
- handlers;
- receiving text;
- calling pipeline;
- formatting response.

Must NOT contain:

- SQL implementation;
- OpenRouter HTTP implementation;
- long prompts.

---

## config.py

Responsibilities:

- environment variables;
- configuration validation;
- model name;
- database path;
- limits.

Required:

TELEGRAM_TOKEN
OPENROUTER_API_KEY

---

## models.py

Internal data structures.

Potential structures:

Material
Analysis
PipelineResult

Use dataclasses where appropriate.

---

## db.py

SQLite persistence.

Initial tables:

materials
analyses
outputs

---

# 6. Database

## materials

Fields:

id
user_id
source_type
raw_text
status
created_at
updated_at

## analyses

Fields:

id
material_id
usefulness
novelty
implementability
monetization
confidence
analysis_json
created_at

## outputs

Fields:

id
material_id
archive_text
client_text
created_at

---

# 7. Pipeline

## Stage 1 — normalize

Input:

raw Telegram text

Output:

NormalizedMaterial

Requirements:

- trim excessive whitespace;
- enforce reasonable size limit;
- preserve meaning;
- don't invent information.

---

## Stage 2 — classify

Determine:

section
type

Allowed sections:

1. Фреймворки промптов
2. Инструменты
3. Системы и схемы
4. Устарело / Потеряло силу
5. На рассмотрении

Allowed types:

Фреймворк
Инструмент
Система
Схема
Разбор

---

## Stage 3 — analyze

Generate:

title
essence
how_to_use
when_useful
limitations
usefulness
verdict

---

## Stage 4 — quality gate

Check:

- concrete problem;
- concrete value;
- practical applicability;
- novelty;
- current relevance;
- implementation feasibility;
- unsupported claims;
- missing information.

Generate:

confidence

---

## Stage 5 — monetization

Generate:

who_pays
what_is_sold
problem
expected_result
mvp
monetization_model
risks

If unclear:

MONETIZATION = UNCLEAR

---

## Stage 6 — save

Save:

original material
analysis
output
status

---

## Stage 7 — return

Telegram receives a concise readable result.

---

# 8. LLM Provider

Create abstraction:

LLMProvider

Initial implementation:

OpenRouterProvider

Requirements:

- timeout;
- HTTP error handling;
- invalid response handling;
- retry only for appropriate transient errors;
- no secret logging.

---

# 9. Prompt architecture

Prompts should be kept in:

prompts.py

Do not scatter large prompt strings across application files.

LLM should return structured JSON where practical.

Application must validate the JSON.

Invalid JSON must not be treated as successful analysis.

---

# 10. Quality scoring

Initial scores:

usefulness: 0–10
novelty: 0–10
implementability: 0–10
monetization: 0–10
confidence: 0–1

Do not pretend these are scientifically validated metrics.

They are internal heuristics.

---

# 11. Anti-Illusion Layer

The system must distinguish:

FACT
INFERENCE
ASSUMPTION
UNKNOWN

Unsupported claims should lower confidence.

---

# 12. Error handling

Possible failures:

Telegram unavailable
OpenRouter timeout
OpenRouter rate limit
OpenRouter invalid response
SQLite error
invalid LLM JSON

For all failures:

- preserve input;
- record failure;
- log safe diagnostic;
- don't claim success.

---

# 13. Retry

Retry only transient failures.

Do not blindly retry:

- invalid API key;
- invalid request;
- malformed prompt;
- invalid application data.

---

# 14. Logging

Log:

timestamp
level
component
event
error type

Never log:

API keys
Telegram token
.env
full private content unnecessarily

---

# 15. Tests

## Unit

Test:

- configuration;
- database initialization;
- material saving;
- material retrieval;
- JSON validation;
- scoring;
- formatting.

## Provider mock

Tests should work without OpenRouter.

## Integration

Test:

pipeline
+
mock provider
+
SQLite

## End-to-end

At least one manual test using real Telegram + OpenRouter.

---

# 16. Acceptance Criteria

MVP is successful when:

[ ] bot starts

[ ] /start works

[ ] text message is received

[ ] material is saved

[ ] OpenRouter request succeeds

[ ] structured result is returned

[ ] result is saved

[ ] restart does not delete data

[ ] API failure does not delete material

[ ] invalid LLM output is handled

[ ] secrets are not logged

[ ] tests pass

---

# 17. Phase 0 — Audit

Before coding:

inspect current workspace.

Find:

- existing bot code;
- brain.py;
- bot_test.py;
- OpenRouter code;
- Genebu code;
- .env;
- requirements;
- memory/state;
- existing databases.

Create:

docs/AUDIT.md

Do not delete anything.

Acceptance:

AUDIT.md exists and identifies reusable code.

---

# 18. Phase 1 — Skeleton

Create:

AGENTS.md
.roo/rules/
bot.py
config.py
db.py
models.py
pipeline.py
prompts.py
providers.py
tests/

Acceptance:

Python imports successfully.

---

# 19. Phase 2 — Telegram

Implement:

/start
text handler

Initially return:

"Material received."

Acceptance:

real Telegram message reaches application.

---

# 20. Phase 3 — SQLite

Save every received material.

Acceptance:

1. send material;
2. verify DB;
3. restart bot;
4. verify material still exists.

---

# 21. Phase 4 — OpenRouter

Implement provider.

Acceptance:

Telegram
→ provider
→ LLM
→ Telegram

---

# 22. Phase 5 — Analysis

Implement structured analysis.

Acceptance:

all required fields exist.

---

# 23. Phase 6 — Quality Gate

Implement confidence and validation.

Acceptance:

invalid/incomplete analysis does not automatically become approved content.

---

# 24. Phase 7 — Monetization

Implement:

WHO_PAYS
WHAT_IS_SOLD
MVP
MONETIZATION_MODEL
RISKS

Acceptance:

bot can distinguish:

clear monetization
unclear monetization

---

# 25. Phase 8 — Images

Only after text MVP works.

Flow:

Telegram photo
→ image extraction/vision
→ normalized material
→ existing pipeline

Do NOT create a second analysis pipeline.

---

# 26. Phase 9 — Documents

Add:

PDF
TXT
DOCX

All become:

NormalizedMaterial

---

# 27. Phase 10 — Search

Start with SQLite capabilities.

Only introduce vector search if real archive size
or retrieval quality demonstrates the need.

---

# 28. Phase 11 — Multi-Agent

Do not implement before evidence.

Potential future agents:

Classifier
Editor
Researcher
Critic
Monetization Analyst

Current implementation:

ordinary Python functions.

---

# 29. Phase 12 — Scaling

Possible future:

SQLite
→ PostgreSQL

Single process
→ workers

Simple pipeline
→ queue

Only after measured need.

---

# 30. Development Rules

After every stage:

1. run tests;
2. inspect errors;
3. verify expected behavior;
4. update documentation;
5. stop at checkpoint.

---

# 31. Decision Rule

If a proposed component does not solve
a demonstrated problem, do not add it.

---

# 32. First Milestone

The first real milestone is NOT:

"multi-agent architecture exists."

The first real milestone is:

A user sends one text to Telegram
and receives a useful, structured,
commercially-oriented analysis,
which remains stored after restart.

---

# 33. Definition of Done

The MVP is DONE when a real user can:

1. open Telegram;
2. send a text;
3. receive an analysis;
4. see monetization possibilities;
5. restart the server;
6. find the material still stored;
7. repeat the process without manual database intervention.
