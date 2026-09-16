# Правила разработки 1С (Unica)

Проект использует MCP-сервер **Unica** для разработки на 1С:Предприятии.
MCP-сервер зарегистрирован в `opencode.json` под именем `unica`.

## Обязательное правило

**Перед выполнением любых действий с метаданными или кодом 1С ОБЯЗАТЕЛЬНО
вызывай соответствующий MCP-инструмент `unica.*`** — вместо ручной правки
файлов JSON/XML/BSL «как получится».

Правила применения:

- Метаданные (объекты, реквизиты, ТЧ, регистры) изменяются ТОЛЬКО через
  `unica.meta.*`.
- BSL-модули и код правится ТОЛЬКО через `unica.code.patch`; чтение и
  навигация — через `unica.code.search`, `unica.code.outline`/`unica.code.graph`,
  `unica.source.*`.
- Формы, роли, СКД, макеты, подсистемы, интерфейс — только через свою группу
  `unica.<категория>.*` (см. карту ниже).
- Каждое изменение завершается валидацией/компиляцией своей группы и сборкой
  `unica.build.make`.
- При невозможности применить инструмент Unica — остановись и уточни причину,
  не обходи правило ручной правкой.

Детальные пошаговые инструкции по навыкам находятся в
`.opencode/skills/<название>/SKILL.md` — при выборе навыка следуй им.

## Карта инструментов (фактическая поверхность сервера Unica 0.12.3, 74 инструмента)

| Категория | Действия |
|---|---|
| `unica.build` | `.make`, `.run`, `.dump`, `.load`, `.update` |
| `unica.project` | `.map`, `.status` |
| `unica.runtime` | `.execute`, `.job.start`, `.job.status`, `.job.wait`, `.job.logs`, `.job.list`, `.job.cancel` |
| `unica.meta` | `.add`, `.edit`, `.info`, `.remove` |
| `unica.code` | `.search`, `.patch`, `.diagnostics`, `.definition`, `.outline`, `.graph` |
| `unica.standards` | `.search`, `.explain` |
| `unica.source` | `.read`, `.locate`, `.resolve`, `.resources`, `.children` |
| `unica.form` | `.add`, `.edit`, `.remove`, `.compile`, `.info`, `.validate` |
| `unica.dcs` | `.compile`, `.edit`, `.info`, `.validate` (СКД) |
| `unica.cfe` | `.init`, `.borrow`, `.patch_method`, `.diff`, `.validate` |
| `unica.cf` | `.init`, `.edit`, `.info`, `.validate` |
| `unica.mxl` | `.compile`, `.decompile`, `.info`, `.validate` |
| `unica.role` | `.compile`, `.edit`, `.info`, `.validate` |
| `unica.subsystem` | `.compile`, `.edit`, `.info`, `.validate` |
| `unica.interface` | `.edit`, `.validate` |
| `unica.template` | `.add`, `.remove` |
| `unica.epf` / `unica.erf` | `.init` |
| `unica.xdto` | `.edit`, `.info` |
| `unica.documentation` | `.search`, `.get` |
| `unica.help` | `.add` |
| `unica.support` | `.edit` |

## Базовый протокол работы

1. `unica.project.map` → `unica.project.status` — определить конфигурацию/расширение и состояние проекта.
2. `unica.source.locate` → чтение `v8project.yaml` — понять структуру и набор источников.
3. `unica.meta.info` + `unica.source.children` — изучить целевые метаданные.
4. `unica.code.search` → `unica.code.definition` / `unica.code.outline` — найти и локализовать код по задаче.
5. Правка через профильную группу (`unica.meta.edit`, `unica.code.patch`, `unica.form.edit`, `unica.role.edit`, `unica.dcs.edit`, `unica.cfe.patch_method` ...).
6. Диагностика и стандарты: `unica.code.diagnostics`, `unica.standards.search`.
7. Валидация/компиляция изменённой группы, затем `unica.build.make`; при необходимости `unica.runtime.execute` / `unica.runtime.job.start`.

## Сценарии разработки

### Стандарты BSL
1. `unica.code.search` — найти проверяемый BSL-код.
2. `unica.code.diagnostics` — прогон диагностик по модулю.
3. `unica.standards.search` → `unica.standards.explain` — уточнить правило при замечании.
4. `unica.code.patch` — применить исправления.
5. `unica.code.diagnostics` — повторный прогон до чистого результата.
6. `unica.build.make` (+ при необходимости `unica.runtime.execute`).

### Шаблоны БСП
1. `unica.code.search` по общим модулям БСП — найти штатный механизм (`УправлениеПечатью`, `РаботаВМоделиСервиса` и т.п.).
2. `unica.meta.info` — проверить доступность подсистем БСП в конфигурации.
3. `unica.source.read` — изучить реализацию метода БСП.
4. `unica.code.patch` — адаптировать вызов под задачу.
5. `unica.code.diagnostics` → `unica.build.make`.
6. Обработки на БСП: `unica.epf.init` (навык `epf-bsp-init`), добавление команды — навык `epf-bsp-add-command`.

### Работа с СКД
1. `unica.dcs.info` — разобрать текущую схему компоновки.
2. `unica.dcs.edit` — внести правки (наборы, группировки, отборы, вычисляемые поля).
3. `unica.dcs.compile` — скомпилировать схему.
4. `unica.dcs.validate` — валидация.
5. При запросной оптимизации: `unica.code.search` + навык `query-optimize`.
6. `unica.build.make` → при необходимости `unica.runtime.execute`.

### Создание расширений CFE
1. `unica.project.map` — определить расширение, в котором работаем (при отсутствии — `unica.cfe.init`).
2. `unica.cfe.borrow` — перенести объекты из поставки.
3. `unica.cfe.patch_method` — патчи методов / изменённые модули (в т.ч. формы).
4. `unica.cfe.diff` — контроль расхождений с поставкой.
5. `unica.build.make` — собрать расширение.
6. `unica.cfe.validate` — финальная валидация расширения.
7. Для основной конфигурации аналогично: `unica.cf.init|edit|info`, `unica.build.make`, `unica.cf.validate`.

### Редактирование управляемых форм
1. `unica.form.info` — изучить структуру формы.
2. `unica.form.edit` — добавить/изменить элементы, реквизиты формы, команды (при создании формы — `unica.form.add`).
3. `unica.code.patch` — правки модуля формы (если нужны обработчики; навыки `form-events`, `object-events`).
4. `unica.form.compile` — компиляция формы.
5. `unica.form.validate` — валидация.
6. `unica.build.make` (при необходимость `unica.runtime.execute`).
7. Командный интерфейс и подсистемы: `unica.interface.edit|validate`, `unica.subsystem.edit|compile|info|validate`.

### Макеты MXL
1. `unica.mxl.info` — получить данные макета.
2. `unica.mxl.decompile` — разложить макет в текстовый DSL.
3. Правка DSL (табличный документ/печатная форма) исходно руками в тексте.
4. `unica.mxl.compile` — собрать макет.
5. `unica.mxl.validate` — валидация.
6. `unica.build.make` → при необходимости `unica.runtime.execute`.

### Роли и права
1. `unica.role.info` (`unica.role.edit`) — просмотр/правка прав роли.
2. `unica.role.compile` — компиляция роли по DSL (`rights.xml`, справка в `.opencode/skills/role-compile/dsl-reference.md`).
3. `unica.role.validate` — валидация.
4. `unica.build.make` → при необходимости `unica.runtime.execute`.
5. Проверка прав для пользователя/ИБ: навыки `db-auth-check`, `security-auth-crypto`.

### Метаданные и проектирование
1. `unica.meta.info` — анализ текущей структуры (регистры, документы, справочники; навыки `register-design`, `metadata-modeling`).
2. `unica.meta.add` / `unica.meta.edit` / `unica.meta.remove` — изменение метаданных.
3. `unica.source.children` / `unica.source.read` — просмотр связанных файлов.
4. `unica.build.make` → `unica.cf.validate` (или `unica.cfe.validate`) → при необходимости `unica.runtime.execute`.

### Запуск и диагностика
1. `unica.build.make` → `unica.runtime.execute` (прогон платформы).
2. `unica.runtime.job.start`/`.status`/`.wait`/`.logs` — регламентные/фоновые задания.
3. `unica.code.diagnostics`, навыки `log-analysis`, `db-performance`, `transactions-locks`, `code-diagnostics`.