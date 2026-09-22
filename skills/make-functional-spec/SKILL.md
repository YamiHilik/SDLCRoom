---
name: make-functional-spec
description: This skill should be used when the user asks to "create a functional spec", "write a system design", "describe the architecture of a feature", "make a functional-spec.md", "describe data flows", "define module boundaries", "document integration points", or wants a technical design document that covers subsystems, data flows, interaction contracts, and integration points — without implementation details.
version: 0.1.0
---

# Make Functional Spec (System Design)

Produce a `functional-spec.md` — a technical design document that describes the shape of a feature without prescribing implementation details.

The document answers five questions:

1. **What are the modules?** — logical subsystems and their responsibilities
2. **How does data flow?** — path from trigger (UI event / timer / external call) to persistence
3. **What do modules say to each other?** — contracts: function signatures, message payloads, API shapes (types and names, not implementations)
4. **What is the business logic?** — rules, conditions, and state machines in prose or diagrams
5. **What does the existing system look like from the outside?** — which services, tables, or OS APIs are touched

## Process

### Step 1 — Gather context

Read everything relevant before writing:
- The product/feature spec if one exists (look for `feature-spec.md` or `*.spec.md` in the project root)
- The knowledge base if one exists (`.docs/knowledge/README.md`)
- Architecture decision records (`.docs/knowledge/adr/`)
- Source files of the existing system that the feature will integrate with

Ask the user for any missing information rather than inventing it. Typical gaps:
- Which existing services/modules are in scope
- Consistency/transaction requirements between modules
- Preferred diagram syntax (Mermaid is the default)

### Step 2 — Draft the document

Use the template in `references/template.md`. Fill every section; mark sections that are genuinely not applicable with a one-line rationale rather than deleting them.

**Modules section** — one sub-heading per module:
- Name (noun phrase, no verbs)
- Responsibility (one sentence)
- What it does NOT own (avoid ambiguity with sibling modules)

**Data flows section** — one flow per user-visible scenario or async trigger:
- Use a Mermaid `sequenceDiagram` or `flowchart` by default
- Label every arrow with the payload type or function name
- Show error / rejection paths if they exist

**Contracts section** — for each inter-module boundary:
- Function / method signatures (language-agnostic pseudocode is fine)
- Message or event structures (field names + types)
- REST / WebSocket / IPC protocol shapes
- Do NOT include implementation bodies

**Business logic section** — for rules that live inside a single module:
- Numbered decision trees, state diagrams (`stateDiagram-v2`), or prose tables
- Make pre/post conditions explicit

**Integration points section** — a table:
| Entity | Type | Access pattern | Owner module |
|--------|------|----------------|-------------|
One row per external service, OS API, database table, or config file touched.

### Step 3 — Review before writing to disk

Present a one-paragraph summary of the design to the user. Highlight any assumptions made where the spec or source was ambiguous. Invite correction before writing the file.

### Step 4 — Write the file

Write `functional-spec.md` to the project root (or the path the user specifies). Use Markdown with Mermaid code blocks for all diagrams.

After writing, print the file path and a two-sentence summary of the design decisions captured.

## Language

Communicate with the user in Russian and write the document in Russian unless the user explicitly requests a different language. Technical terms, identifiers, and code inside code blocks remain in their original form (English).

## Style rules

- Present tense throughout ("The AudioCapture module records…", not "will record")
- No implementation language in section headers; pseudocode only inside code blocks
- Every diagram must have a title comment (`%% Title`) on the first line
- Keep the document under 600 lines; if it grows larger, split into per-module sub-documents and link from a top-level index

## Additional resources

- **`references/template.md`** — blank document template to copy-fill
