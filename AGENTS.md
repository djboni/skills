## About me

- Embedded C developer.
- Speak Portuguese and English fluently.

## Communication

- Be concise. Don't oversell changes.
- If your last message included URLs, list them at the end so I can open them easily.

## Code

- Only ASCII in code. Comments with language accents are acceptable. Use uA, kOhm, ->, !=, etc.

## Working with Git

- Always use semantic commit prefixes (feat:, fix:, docs:, etc.).
- Only ASCII in commit messages. Language accents are acceptable.

## HTML

- Use UTF-8 in HTML web pages freely.

## Memory

Keep durable facts about the current project in `.scratch/mem/` at the project root: a `MEMORY.md` index plus one topic file per topic.

- **Index** — `MEMORY.md` is a list, one line per topic (`- <slug>: one-line description`). Read it in full at session start; keep it under ~150 lines, moving detail into topic files and merging or dropping lines as it grows. Topic files are read on demand as the conversation touches them.
- **Topic files** — `.scratch/mem/<slug>.md` holds one topic's durable facts: build or flash commands, conventions, architecture decisions, constraints, preferences or clarifications of mine (quoted), or the open state of a todo list. Create a file when a topic's first durable fact appears; append as facts crystallize. Frontmatter `topic`, `project`, `date`, `updated`, `description`; refresh `updated` on every write and use absolute dates in YYYY-MM-DD format in prose.
- **Capture** — write a fact the moment it is durable, or when I say "remember this". Keep the current task's Todo in `.scratch/mem/todo.md` and update it as you work, so a compacted session can restore it.
- **Resume** — after context compaction, re-read the index and restore the open Todo before continuing work.
- **Subagents** — a subagent is not a session: it never creates, names, or chooses a memory file. The parent designates the exact topic-file path in the task prompt, and the subagent writes its durable facts to that file directly (including Todo updates); it creates the file first only when instructed. In nested delegation, the top-level session designates transitively.
- **Consolidate** — when the store grows (say >10 topic files) or the index nears its budget, offer a consolidation pass: merge duplicate topics, mark superseded facts (quoting the newer one), and confirm every deletion with me. Nothing is deleted automatically.
