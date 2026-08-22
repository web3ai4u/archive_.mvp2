# Architecture Rules

## Основная архитектура

Telegram
↓
bot.py
↓
pipeline.py
↓
providers.py
↓
OpenRouter

                 ↓
               db.py
                 ↓
              SQLite

## Ответственность модулей

bot.py
Telegram transport.

config.py
Configuration.

models.py
Data structures.

pipeline.py
Business workflow.

providers.py
External LLM providers.

prompts.py
LLM prompts.

db.py
Persistence.

tests/
Automated tests.

## Запрещённая связанность

bot.py не должен напрямую работать с SQL.

bot.py не должен напрямую обращаться к OpenRouter HTTP API.

pipeline.py не должен зависеть от Telegram Update.

providers.py не должен содержать Telegram logic.

prompts.py не должен содержать database logic.

## Расширение

Будущие adapters:

Text
Image
PDF
URL
RSS

должны приводить вход к единому внутреннему представлению.

Не создавать отдельный pipeline для каждого типа входа.

## Масштабирование

Сначала:

SQLite.

Затем, только при необходимости:

PostgreSQL.

Сначала:

обычные Python functions.

Затем, только при необходимости:

multi-agent.

Сначала:

SQLite search/full text.

Затем, только при необходимости:

vector search.
