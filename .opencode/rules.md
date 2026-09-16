# Правила разработки 1С (Unica)

Проект использует MCP-сервер **Unica** для разработки на 1С:Предприятии.
MCP-сервер зарегистрирован в `opencode.json` под именем `unica`.

## Обязательное правило

При выполнении любых задач, связанных с 1С:Предприятием, автоматически
используй **соответствующие MCP-инструменты с префиксом `unica.`** вместо
ручной правки файлов JSON/XML/BSL «как получится». Инструменты Unica
валидируют изменения, учитывают стандарты и выполняют компиляцию.

Детальные пошаговые инструкции по каждому навыку хранятся в
`.opencode/skills/<название>/SKILL.md` — при выборе навыка следуй им.

## Карта инструментов `unica.<категория>.<действие>`

| Категория | Доступные действия |
|---|---|
| `unica.project` | `.map`, `.status` |
| `unica.build` | сборка конфигурации/расширения |
| `unica.runtime` | `.execute`, `.job` (запуск 1С, регламентные задания) |
| `unica.code` | `.search`, `.patch`, `.diagnostics`, `.definition`, `.outline`, `.graph` |
| `unica.meta` | `.add`, `.edit`, `.info`, `.remove` |
| `unica.standards` | `.search`, `.explain` |
| `unica.source` | `.read`, `.locate`, `.resolve`, `.resources`, `.children` |
| `unica.form` | `.add`, `.edit`, `.remove`, `.compile`, `.info`, `.validate` |
| `unica.dcs` | `.compile`, `.edit`, `.info`, `.validate` (СКД) |
| `unica.cfe` | `.init`, `.borrow`, `.patch`, `.diff`, `.validate` (расширения) |
| `unica.cf` | `.init`, `.edit`, `.info`, `.validate` (конфигурация) |
| `unica.mxl` | `.compile`, `.decompile`, `.info`, `.validate` (макеты) |
| `unica.role` | `.compile`, `.edit`, `.info`, `.validate` (роли) |
| `unica.subsystem` | `.compile`, `.edit`, `.info`, `.validate` |
| `unica.interface` | `.edit`, `.validate` (интерфейс командного интерфейса) |
| `unica.template` | `.add`, `.remove` |
| `unica.epf` / `unica.erf` | `.init` (внешние обработки/отчёты) |
| `unica.xdto` | `.edit`, `.info` (XDTO-пакеты) |
| `unica.documentation` | `.search`, `.get` |
| `unica.help` | `.add` (справочная информация) |
| `unica.support` | `.edit` (правила поддержки) |

## Базовый протокол работы

1. **Ориентация в проекте** — `unica.project.map`, `unica.project.status`,
   чтение `v8project.yaml`, `unica.source.locate`.
2. **Изучение метаданных** — `unica.meta.info`, `unica.source.children`.
3. **Поиск кода** — `unica.code.search`, `unica.code.definition`, `unica.code.outline`.
4. **Правка** — `unica.meta.edit`, `unica.code.patch`, `unica.form.edit`,
   `unica.role.edit`, `unica.dcs.edit`, `unica.cfe.patch` и т.д.
5. **Проверка/компиляция** — `unica.*.validate` и `unica.*.compile` для
   изменённого объекта (форма, роль, СКД, MXL, подсистема).
6. **Диагностика и стандарты** — `unica.code.diagnostics`, `unica.standards.search`.
7. **Сборка и запуск** — `unica.build`, `unica.runtime.execute` / `unica.runtime.job`.

## Сценарии разработки

### Стандарты BSL
- Проверяй код на соответствие стандартам: `unica.standards.search`,
  `unica.standards.explain`, `unica.code.diagnostics`.
- Исправляй замечания `unica.code.patch`; навык `code-diagnostics`,
  `code-review`, `code-patch`.

### Шаблоны БСП
- При решении типовых задач опирайся на подсистемы БСП: `unica.code.search`
  по общим модулям БСП, `unica.meta.info`, `unica.source.read`.
- Навыки: `bsp-patterns`, `form-patterns`, `epf-bsp-init` (внешняя обработка на
  БСП), `epf-bsp-add-command` (команда обработки», `form-events`, `object-events`.

### Работа с СКД
- Редактирование схемы: `unica.dcs.edit`; компиляция: `unica.dcs.compile`;
  анализ: `unica.dcs.info`; проверка: `unica.dcs.validate`.
- Оптимизация запросов СКД: `unica.code.search`, навык `query-optimize`,
  `dcs-compile`, `dcs-edit`, `dcs-info`, `dcs-validate`.

### Создание расширений CFE
- Создание расширения: `unica.cfe.init`; перенос объектов из поставки:
  `unica.cfe.borrow`; патчи методов: `unica.cfe.patch`; сравнение:
  `unica.cfe.diff`; проверка: `unica.cfe.validate`.
- Работа с собственной конфигурацией через `unica.cf.init|edit|info|validate`.
- Навыки: `cfe-init`, `cfe-borrow`, `cfe-patch-method`, `cfe-diff`,
  `cfe-validate`, `cf-init`, `cf-edit`, `cf-info`, `cf-validate`.

### Редактирование форм
- Форма управляемая/обычная: `unica.form.add`, `unica.form.edit`,
  `unica.form.remove`; компиляция формы: `unica.form.compile`;
  проверка: `unica.form.validate`; анализ: `unica.form.info`.
- Обработчики и события: навыки `form-events`, `object-events`, `form-patterns`.
- Командный интерфейс и подсистемы: `unica.interface.edit|validate`,
  `unica.subsystem.edit|compile|info|validate`.

### Макеты MXL
- Открытие и анализ макета: `unica.mxl.info`, `unica.mxl.decompile`;
  правка текстового DSL: `unica.mxl.compile`, проверка `unica.mxl.validate`.
- Табличные документы и печатные формы через MXL-макеты. Навыки:
  `mxl-compile`, `mxl-decompile`, `mxl-info`, `mxl-validate`.

### Роли и права
- Правка/просмотр прав: `unica.role.edit`, `unica.role.info`;
  компиляция: `unica.role.compile`; проверка: `unica.role.validate`.
- Файлы ролей — `rights.xml` (DSL-описание в `role-compile/dsl-reference.md`).
- Навыки: `role-compile`, `role-edit`, `role-info`, `role-validate`,
  `db-auth-check`, `security-auth-crypto`.

### Прочее
- Метаданные: `unica.meta.add|edit|remove|info`; проектирование регистров —
  навык `register-design`, `metadata-modeling`.
- Внешние обработки/отчёты: `unica.epf.init`, `unica.erf.init`;
  шаблоны: `unica.template.add|remove`; XDTO: `unica.xdto.edit|info`.
- Запуск и диагностика: `unica.runtime.execute|job`, навыки `v8-runner`,
  `db-performance`, `transactions-locks`, `log-analysis`, `code-diagnostics`.