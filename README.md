# Unica1C

Репозиторий для разработки на платформе **1С:Предприятие** с использованием
MCP-сервера **Unica** и ассистента **OpenCode**.

## О проекте

Проект организован как рабочее пространство разработки 1С, в котором:

- хранится выгрузка конфигурации/расширения (в отдельных файлах);
- подключён MCP-сервер **Unica** (v0.12.3), предоставляющий ассистенту
  набор инструментов `unica.*` для работы с метаданными, BSL-кодом, СКД,
  формами, макетами, ролями и сборкой;
- применяются стандарты BSL и шаблоны БСП через сводный регламент
  `.opencode/rules.md` и набор навыков `.opencode/skills/`.

## Структура репозитория

```
├── src/                  # файлы выгрузки конфигурации/расширения
├── build/                # скомпилированные артефакты: .cf, .cfe, .epf
├── temp/                 # временные файлы, дампы, логи (не для Git)
├── .opencode/
│   ├── rules.md          # сводный регламент разработки (алгоритмы unica.*)
│   └── skills/           # навыки Unica (76 навыков, SKILL.md)
├── opencode.json         # локальная конфигурация OpenCode (MCP-сервер unica)
└── README.md
```

Содержимое `src/`, `build/` и `temp/` игнорируется Git (кроме `.gitkeep`),
см. `.gitignore`.

## Окружение: OpenCode + Unica

### MCP-сервер Unica

Плагин Unica установлен во внешний каталог машины:

```
C:\Users\popov\.unica\unica-marketplace\
```

и зарегистрирован в `opencode.json` как локальный MCP-сервер `unica`
(команда `unica-bootstrap run --plugin-root <путь к плагину>`). Рантайм ядра
скачивается автоматически и кешируется в `C:\Users\popov\.codex\unica\runtimes`.

Инструменты MCP-сервера доступны ассистенту с префиксом `unica.` — например,
`unica.meta.edit`, `unica.form.edit`, `unica.dcs.compile`, `unica.role.compile`.

### Навыки и правила

- **Навыки** — `.opencode/skills/`: полный набор навыков Unica для 1С
  (код, метаданные, СКД, CFE, формы, MXL, роли, подсистемы, СКД-компиляция
  и т.д.). Каждый навык описан в `SKILL.md`.
- **Правила** — `.opencode/rules.md` подключаются через
  `"instructions": [".opencode/rules.md"]` и содержат:
  - обязательное правило: перед любыми действиями с метаданными/кодом 1С
    вызывать соответствующий MCP-инструмент `unica.*`;
  - карту фактической поверхности инструментов (74 инструмента сервера 0.12.3);
  - базовый протокол работы и нумерованные алгоритмы по сценариям
    (стандарты BSL, шаблоны БСП, СКД, расширения CFE, редактирование форм,
    макеты MXL, роли, метаданные, запуск и диагностика).

> После изменения `opencode.json`, правил или навыков необходимо
> перезапустить OpenCode.

## Основные сценарии разработки

1. **Ориентация** — `unica.project.map` / `unica.project.status`,
   чтение `v8project.yaml`.
2. **Метаданные** — `unica.meta.info`, `unica.meta.add|edit|remove`.
3. **Код и стандарты BSL** — `unica.code.search`, `unica.code.patch`,
   `unica.code.diagnostics`, `unica.standards.search`.
4. **СКД** — `unica.dcs.edit` / `unica.dcs.compile` / `unica.dcs.validate`.
5. **Расширения CFE** — `unica.cfe.init`, `unica.cfe.borrow`,
   `unica.cfe.patch_method`, `unica.cfe.validate`.
6. **Формы** — `unica.form.edit`, `unica.form.compile`, `unica.form.validate`.
7. **Макеты MXL** — `unica.mxl.decompile`, `unica.mxl.compile`,
   `unica.mxl.validate`.
8. **Роли** — `unica.role.edit`, `unica.role.compile`, `unica.role.validate`.
9. **Сборка и запуск** — `unica.build.make`, `unica.runtime.execute`,
   `unica.runtime.job.*`.

Точные последовательности вызовов — в `.opencode/rules.md`.

## Начало работы

1. Склонировать репозиторий.
2. Установить плагин Unica (см. раздел «Окружение»).
3. Запустить OpenCode в корне проекта — MCP-сервер `unica`, навыки и правила
   подхватываются автоматически.
4. Выполнять задачи разработки 1С через инструменты `unica.*`.