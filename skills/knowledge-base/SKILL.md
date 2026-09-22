---
name: knowledge-base
description: Use this skill whenever you work in this repository — before answering questions about how anything in the project works, before writing or modifying code, and after finishing any non-trivial change. The project keeps its source of truth in `.docs/knowledge/` (a wiki-style knowledge base with `README.md` as the map) and `.docs/knowledge/adr/` (architecture decision records). Trigger this skill on phrases like "how does X work", "where is Y", "add feature", "refactor", "fix bug", "what's the convention for", or any request that touches the codebase's behavior, architecture, or conventions. Also trigger after making changes — the knowledge base must stay in sync with the code, and new architectural decisions must be captured as ADRs. Do NOT skip this skill assuming you can figure things out from the code alone; the knowledge base captures intent and decisions that the code does not.
---

# Project Knowledge Base

This project maintains a living knowledge base. Your job is to **read it before acting**, **keep it in sync** when you change things, and **record architectural decisions** as ADRs.

## Where things live

```
.docs/
└── adr/
    ├── <YYYY-MM-DD>-<short-title>.md
    ├── ...
    └── outdated/          ← superseded ADRs go here
└── knowledge/
    ├── README.md              ← map of the entire knowledge base
    ├── <topic>.md             ← one document per topic
    ├── ...
```

- `.docs/knowledge/README.md` is the **entry point**. It links to every document. Read it first.
- Each topic (design system, components, routing, data models, etc.) is its own `.md` file.
- Documents reference each other with wiki-style `[[link]]` syntax — the link text matches the filename without extension.
- `adr/` holds Architecture Decision Records. **ADRs are immutable.**

## When to consult the knowledge base

**Before answering or coding, always read `.docs/knowledge/README.md` first.** Then read the specific documents relevant to the task. The README is small and gives you the map; skipping it means you'll miss context that's already been written down.

Concretely, consult before:

- Answering questions about how the project works ("how does the kanban board behave?", "what's the data model for tasks?")
- Implementing a feature — read the relevant component, design system, and routing docs
- Fixing a bug — read the doc for the affected area to understand intended behavior
- Refactoring — check ADRs in `.docs/adr/` for decisions that constrain your options

If a document is missing for the area you need, that itself is a signal: either the area is undocumented (note it, and consider adding a doc) or the README is out of sync.

## When to update the knowledge base

After making any change that affects what's documented, update the knowledge base in the same task. Treat the docs the way you treat tests — they're part of "done".

Update when you:

- Change behavior, structure, or conventions described in an existing document → edit that document
- Add a new feature, component, or subsystem that no existing document covers → create a new document and add a link in `README.md`
- Rename or delete something referenced in the docs → fix every reference (search for the old name across `.docs/knowledge/`)
- Make a non-trivial architectural choice → write an ADR (see below)

Do not let docs drift. If the code says one thing and the doc says another, the doc is a bug.

### What belongs in knowledge base docs vs ADRs

База знаний и ADR описывают **систему**. Всё, что описывает сам документ или
историю его правки, в них не попадает.

Не добавлять:

- обоснования собственных правок: «здесь не используется термин X», «переименовано, потому что»,
  «раздел переписан», «уточнено по итогам ревью»;
- повторное изложение того, что уже сказано в контракте, таблице ответственности или диаграмме
  этого же документа — ради связности или подстраховки;
- определения понятий, очевидных из контракта.

**Проверка перед добавлением абзаца или раздела:** проверяется ли это утверждение по коду
и поведению системы? Если оно проверяется только по истории правки документа или по переписке
о нём — это не содержание документа.

**Что и почему изменено в документе** — это сообщение коммита.

**Knowledge base doc** — только актуальное описание системы:
- как работает фича / поток / компонент прямо сейчас
- бизнес-правила и их условия
- нетривиальные ограничения или хрупкие места ("GitLab присылает X в ситуации Y — код делает Z")

**ADR** — всё остальное про "почему":
- история расследований и отладки
- хронология багов и инцидентов
- альтернативы, которые рассматривались
- причина, по которой текущий код написан именно так

Конкретный тест: если удалить секцию из knowledge base doc, станет ли непонятно как работает система? Если нет — это ADR, не knowledge base.

### Adding a new document

1. Pick a clear, lowercase, hyphenated filename matching the language already used in the knowledge base (e.g., `card-component.md`, `карточка-дела.md` — match the existing convention).
2. Create the file in `.docs/knowledge/`.
3. Add a bullet for it in the appropriate section of `.docs/knowledge/README.md` using the same `[[link]] — short description` format as the surrounding entries.
4. If it fits no existing section, add a new section to the README rather than burying it.

### Editing an existing document

- Keep the document focused on its topic. If the change is really about a different topic, edit that other document (or create a new one) instead of stuffing unrelated content in.
- Preserve the existing structure and tone. The knowledge base is meant to be browsable; consistent shape matters.
- Update cross-references if you rename headings other documents link to.

## Architecture Decision Records (ADRs)

ADRs live in `.docs/adr/` and capture *why* a decision was made, not just *what* the code does.

### Hard rules

- **ADRs are never edited.** Once written, the file stays as it is — it's a historical record.
- **To change a decision, write a new ADR** that supersedes the old one, then move the old one to `.docs/adr/outdated/`. Do not delete it.
- **One decision per ADR.** Keep the scope minimal — a single, narrow concern. If you find yourself writing about two things, split them.
- **Filename format:** `<YYYY-MM-DD>-<short-meaningful-title>.md` (e.g., `2026-05-10-use-postgres-jsonb-for-task-metadata.md`).

### When to write an ADR

Write one when you make a choice that:

- Constrains future work (technology choice, schema shape, API contract, library selection)
- Has reasonable alternatives that someone might later wonder about
- You'd want a future developer (including yourself in six months) to understand the reasoning behind

Don't write ADRs for trivial choices (variable names, file layout within a feature, fixing typos). Reserve them for decisions worth re-explaining.

### ADR template

Keep it short. A good ADR is half a page, not three pages.

```markdown
# <Title matching the filename>

- **Date:** YYYY-MM-DD
- **Status:** Accepted

## Context
What problem are we solving? What constraints apply?

## Decision
What did we choose? State it plainly.

## Consequences
What follows from this — both good and bad. What does it lock us out of?

## Alternatives considered
Briefly: what else was on the table, and why not.
```

### Superseding an ADR

When a previous decision no longer holds:

1. Write a new ADR with today's date that states the new decision and references the old one by filename in its Context section.
2. `git mv .docs/adr/<old-adr>.md .docs/knowledge/adr/outdated/`
3. Do not edit the old ADR's content — moving it is enough.

## Workflow summary

For any non-trivial task in this repo:

1. **Read** `.docs/knowledge/README.md`, then the topic documents that touch your task, then any relevant ADRs.
2. **Do the work.**
3. **Update** the affected documents, or add new ones plus a README entry.
4. **Write an ADR** if you made an architectural choice. Move any superseded ADR to `outdated/`.

If you're unsure whether a doc update or ADR is warranted, lean toward writing it — undocumented decisions are the expensive ones.
