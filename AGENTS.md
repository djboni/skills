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

Keep durable facts about the current project in `.scratch/mem/<NNNN>-<topic>.md` at the project root (the working directory where the top-level session started).

- **Capture** — a fact becomes a memory the moment it is durable: a build or flash command, a code convention, an architecture decision, a don't-touch constraint, a preference or clarification of mine (quoted), or the current state of your todo list. Keep the Todo section current as you work so a compacted session can resume it.
- **File** — one memory file per session. Append to the newest memory file when its topic matches this session or its Todo list is still open; otherwise start the next file. Number files from 0001, monotonic; slug the filename from the session's theme; skip the file when the session captured nothing durable.
- **Subagents** — a subagent is not a session. It never creates, numbers, or chooses a memory file on its own. The parent always designates the complete target path (number and slug) in the task prompt; the subagent writes its durable facts to that file directly, including Todo updates, and creates the file only when the parent instructed so (e.g., a new path for unrelated work). The parent may assign a different file when the subagent's work is unrelated to the session's main thread. In nested delegation, the top-level session designates transitively. A subagent reads the store only on demand; the parent passes relevant memory context in the prompt.
- **Shape** — frontmatter (`number`, `project`, `date`, `topic`, `description`), then sections Commands, Conventions, Architecture, Constraints, Clarifications, Todo. Cite the source file and line under facts backed by code. Write in English.
- **Read** — at session start, list `.scratch/mem/`, read the newest five files (all if fewer), open more when the task touches them, and re-read after context compaction.
