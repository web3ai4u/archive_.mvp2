# Testing Rules

## Минимальная проверка

После изменения Python:

python3 -m py_compile ...

## Unit tests

Тестировать отдельно:

- configuration;
- database;
- normalization;
- classification;
- formatting;
- validation.

## Integration tests

Проверять:

Telegram
→ pipeline
→ provider
→ database

по возможности через mocks.

## Не зависеть от реального LLM

Большинство automated tests не должны каждый раз обращаться
к OpenRouter.

Использовать mocked provider.

## End-to-end

Периодически выполнять реальный тест:

Telegram
→ OpenRouter
→ SQLite
→ Telegram.

## Regression

Если найден bug:

1. создать test, воспроизводящий bug;
2. исправить код;
3. убедиться, что test проходит.

## Completion criteria

Задача считается выполненной только если:

- код импортируется;
- syntax проверен;
- tests проходят;
- основной сценарий работает;
- ошибки обработаны.
