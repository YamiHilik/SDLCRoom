# SDLCRoom

Агенты и скиллы для **Agentic SDLC** — конвейер от сбора требований до закоммиченного, протестированного кода в [Oh My Pi](https://github.com/can1357/oh-my-pi) (OMP).

## Структура

```
SDLCRoom/
├── agents/          # агенты (системные промпты)
│   ├── business-analyst.md
│   └── developer.md
└── skills/          # скиллы: <name>/SKILL.md (+ references/)
    └── ...
```

## Агенты

### `business-analyst` — Бизнес-системный аналитик

Цель: довести постановку до полной ясности, спроектировать решение и разбить его на атомарные задачи, готовые к выполнению разработчиком. Код не пишет — производит артефакты.

Конвейер (по скиллам):

1. `grill-me` / `grill-with-docs` — сбор требований, вопросы строго по одному.
2. `domain-modelling` — доменная модель, глоссарий, ADR.
3. `make-research` → `research.md`
4. `make-functional-spec` → `functional-spec.md`
5. `make-ui-spec` → `ui-spec.md` (если есть UI)
6. `review-specs` — валидация спецификаций.
7. `make-plan` → `implementation-plan.md`
8. `review-plan` — валидация плана.
9. `make-tasks` → атомарные задачи в `tasks/`.
10. `review-tasks` — финальная проверка задач.
11. `estimate-scrum-points` — оценка в стори-поинтах (по запросу).

### `developer` — Разработчик

Выполняет задачи **строго по одной**: разработать → прогнать все проверки и тесты → код-ревью → приёмка → только потом следующая. Работает как супервизор: реализацию и ревью делегирует субагентам.

Цикл (по скиллам):

- `do-task-one` — разработка через субагентов + код-ревью + приёмка.
- `commit-message` — итоговый commit-message.
- `simplify` — упрощение и очистка кода.
- `send-push-to-user` — уведомление о завершении.
- `knowledge-base` — синхронизация базы знаний.

## Скиллы

| Категория | Скиллы |
|---|---|
| Анализ | `grill-me`, `grill-with-docs`, `domain-modelling`, `knowledge-base` |
| Проектирование | `make-research`, `make-functional-spec`, `make-ui-spec`, `make-plan` |
| Валидация | `review-specs`, `review-plan`, `review-tasks` |
| Задачи / оценка | `make-tasks`, `estimate-scrum-points` |
| Разработка | `do-task-one`, `simplify`, `commit-message`, `send-push-to-user` |

## Установка

Скопируй в конфиг OMP:

```bash
# скиллы
mkdir -p ~/.omp/agent/skills
cp -R skills/* ~/.omp/agent/skills/

# агенты
mkdir -p ~/.omp/agent/agents
cp agents/*.md ~/.omp/agent/agents/
```

Для проекта (только внутри его репозитория): `<cwd>/.omp/skills/` и `<cwd>/.omp/agents/`.

После копирования перезапусти OMP или открой новую сессию — агенты и скиллы подхватываются при старте.

## Использование

Вызови агента по имени через task-инструмент:

- `business-analyst` — собрать требования, спроектировать решение, нарезать задачи.
- `developer` — выполнить задачи по одной.

Скиллы доступны модели через `skill://<name>` и команду `/skill:<name>`.

## Лицензия

Apache License 2.0 — см. `LICENSE`.
