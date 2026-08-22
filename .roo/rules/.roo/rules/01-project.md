# Security Rules

## Secrets

Никогда не сохранять API keys в коде.

Использовать environment variables.

Не печатать secrets.

Не отправлять secrets пользователю.

## .env

.env никогда не должен попадать в Git.

Проверять .gitignore.

## Telegram

Не считать Telegram user input доверенным.

Проверять:

- длину;
- тип;
- размер;
- допустимые значения.

## LLM output

LLM output является недоверенным внешним содержимым.

Не выполнять его как:

- Python;
- shell;
- SQL;
- system command.

Не интерпретировать текст LLM как инструкции операционной системе.

## Database

Использовать parameterized queries.

Не строить SQL через небезопасную конкатенацию пользовательского ввода.

## Logging

Логи не должны содержать:

- API keys;
- Telegram tokens;
- полный .env;
- приватные данные без необходимости.

## Files

Не удалять пользовательские файлы автоматически.

Не выполнять rm -rf без явного разрешения.

## External services

Любой внешний API считать потенциально недоступным.

Обрабатывать:

- timeout;
- HTTP errors;
- invalid response;
- rate limit;
- malformed data.

## Principle

Security нельзя сокращать ради простоты архитектуры.
