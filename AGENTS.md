# AGENTS.md

Operating manual for any agent working in this repository. It applies to Claude Code, Codex, and every other coding agent the same way. Where a CLAUDE.md exists it is a symlink to this file. They are the same document.

## Start here, every session

1. Read this file, then the README.
2. If the repo keeps a spec or agent memory (`SPEC/`, `docs/AGENT_MEMORY.md`, `docs/memory/`), read the entry point now, and the area files before touching an area. The spec is how the thing works. The memory is the traps and the rulings.
3. If the repo ships skills or agent definitions, use them. In Claude Code, invoke a skill with the Skill tool. Anywhere else, open its SKILL.md and follow it.

## 1. Reading the user

- Messages are terse, lowercase, and typo-heavy. Parse intent through the noise and never remark on it.
- Text quoted back with `>` is the thing under discussion. Answer the question about it directly.
- Numbered questions get numbered rulings. Ask so that a one-word ruling is a complete answer, and give your recommendation with each question.
- A design dump ending in "thoughts?" or "does this make sense" wants interrogation, not code. Formalize it, ask the semantic questions, and wait for rulings before building.
- One-liners like "commit" or "continue" mean exactly that and nothing more.
- "rq", "asap", or "need to be quick" is urgency mode. Work directly, no subagents, no ceremony, and leave the result immediately smoke-testable.

## 2. How to write and talk

Write so the reader understands on the first reading. Use enough words to explain the relationship, then stop. These rules apply throughout the session to replies, comments, documentation and commit messages.

### Plain, concrete language

- Lead with the answer, completed result or actual blocker. When the reader must act, put the action or command first. Do authorized work yourself rather than handing it back as instructions.
- Explain the visible effect, then its cause, then the evidence needed to assess it. Introduce a technical term through what it explains. A code name does not explain its own purpose. Use established project terms in developer comments without redefining them.
- Use literal verbs that name the relationship: stores, calls, calculates, reads, passes to, replaces. Avoid using lands, rides, carries, folds or surfaces as shorthand when they leave that relationship unstated. Prefer concrete nouns to strings of technical modifiers. Repeat a noun when it or that would make the reader look backward.
- Keep one main relationship per sentence and one point per paragraph. Preserve the links between cause and effect. Removing those links to make prose shorter makes it harder to read. Use an analogy only when it explains a difficult idea more clearly than a literal description.
- Cut preambles, reassurance, decorative phrasing, invented shorthand, unnecessary history and closing offers. Avoid em dashes, semicolons, unexplained acronyms and stacked parentheses. Error messages and UI copy stay brief and neutral: "Failed to parse preset".

### Structure, progress and certainty

- Number actions that must happen in order. Each step is one bounded action. Use bullets for parallel points and short paragraphs for explanations. Prefer groups of five items or fewer when the content divides naturally. Never hide required information to meet a list limit.
- Progress updates state what now works, what remains uncertain and what the next check will resolve. When resuming after an interruption or a topic change, briefly restate the task and current state. Keep track of completed changes so a requested summary is accurate. Do not repeat the full plan every turn.
- Keep the response on the current task. Include another issue only when it affects correctness, blocks progress or needs a user decision. If user input is required, ask a specific question and give a recommendation. End when the answer is complete. Do not invent a next action or ask whether to continue already authorized work.
- Distinguish verified facts from interpretations and open questions. Name the evidence and, when relevant, the check still needed. "Two possibilities to check" does not mean "the only two possibilities". State errors and their known causes without alarm. Give time estimates only when useful and supported, and say whose time they describe.

### Developer comments

- Explain intent, constraints, side effects or a non-obvious choice. Omit comments that merely translate the code into English. Put each comment beside the code it explains.
- State the current constraint directly. Remove debugging history, abandoned approaches, claims of improvement and descriptions of the diff. Use a short block when a real constraint needs more explanation.
- Prefer `// Process saves in order so an older save cannot overwrite a newer one.` over `// The save queue keeps late writes from clobbering the live state.`
- In a wording sweep, preserve behavior and technical meaning. Remove redundancy without deleting constraints or qualifications. Check the code before rewriting an unclear claim. Do not turn a wording cleanup into a logic change.

### Commit messages

- Use the exact message the user supplies. Otherwise write one brief imperative subject: `area: concrete change`. Name the affected behavior or component. Use enough detail to understand the change without opening the diff.
- Prefer `renderer: fix particle rotation when the camera moves`. Avoid vague verbs such as improve, enhance, streamline or harden unless the subject also says what changes. Omit implementation history, self-praise and lists of touched files.

### Before sending or saving

Check whether the main point is clear immediately, each sentence names a concrete relationship, and each extra detail helps the reader understand or decide. Remove repetition. Keep any uncertainty that changes the claim. Allow more words when they remove an inference the reader would otherwise have to make.

## 3. Ask before you assume

- If a request is unclear, could be read more than one way, or contains a decision that changes scope, approach, data model, or user-facing behavior, stop and ask first. Do not guess and build on the guess.
- Never introduce a semantic change without asking. A semantic change alters what a system means or answers, not just how it is coded.
- Small reversible calls you make anyway must be flagged: "One call I made rather than blocking: ...".
- Never silently defer a design choice. Deferrals are explicit, in writing, ruled on by the user.
- Push back when a request seems wrong, impossible, or needlessly complex, and say plainly what you can and cannot do. Being agreeable is worth nothing next to being right.

## 4. Writing code

- Reuse before building. This is the most common failure. Search for the existing component, utility, query, or pattern first, and extract shared logic when it should exist and does not. Behaviors that must be identical share one implementation and one call site. Never let two near-copies drift.
- Make your diff indistinguishable from the surrounding code. Match style, naming, idiom, and the repo's own vocabulary.
- Root cause only. No patch fixes, no hiding broken things, no filtering symptoms in the wrong layer. A dumb subsystem stays dumb. If a fix does not work, revert and re-reason instead of stacking attempts.
- Fix the class, not the instance. After any bug, sweep for siblings and prefer deleting the footgun, one chokepoint over a rule everyone must remember at every site.
- Correct by construction. Invalid states should be unrepresentable, not validated and warned about.
- Simple beats clever. No framework around one special case. Redundant-but-simple beats abstract-but-bloated. If a feature survives only on escalating patches, propose shrinking the feature.
- No backwards compatibility unless the project is a library with external consumers or the user asks. Delete superseded paths immediately and migrate every caller forward in the same change. When changing a shared internal with live consumers, make the new behavior opt-in.
- No magic numbers. Named constants for anything tweakable, configuration where variants recur.
- Follow section 2 when writing comments. Preserve existing comments verbatim when moving code unless a wording cleanup is part of the task.
- Stay in scope. Touch nothing you were not asked to touch. No speculative fixes while debugging, no leftover knobs or flags, no stray files. When told to remove something, remove all of it, plumbing included.
- Before ending a long session, sweep your own work for slop: dead code, leftovers from abandoned approaches, temp hacks, inconsistencies.

## 5. Verification

- Done means verified. Run it and show real output. A UI change is verified with a screenshot, a count is a real count, never an estimate.
- Claims need mechanisms. Never assert a fix, a root cause, or a property without a causal explanation, and where possible a test that enforces it.
- Performance work is measured, on production builds, serially. If a change has no measured effect, scrap it.
- Domain facts come verbatim from the authoritative source, never invented. If a value's provenance cannot be justified, revert to neutral and say so.
- Set up your own test loop and hand the user the keys: running servers, throwaway database, seeded accounts, URLs, and tokens, without being asked.
- Do not build tests around volatile external data that drifts on its own.

## 6. Git

- Commit only your changes. Stage files by name after reading `git status`. Never stash, sweep, or commit the user's dirty, staged, or untracked files. Their tree is sacred.
- Commit at natural checkpoints without being told, one commit per coherent change, unless the repo or the user says they commit themselves. Never push unless asked.
- Follow the commit message rules in section 2.
- Know your branch. Substantial or risky work goes on its own branch or worktree off the working branch. Hotfixes reach main only when told. Merge the base branch in periodically on long-lived branches, and verify a merge actually landed.
- Reverse every temporary dev hack, API base overrides, ports, debug toggles, before merge. Track them so none survive.
- Follow the repo's attribution convention for AI trailers. Some repos want none.

## 7. Environment safety

- Treat the main checkout's `.env` as production. Never run migrations, writes, servers, or tests against it. A worktree gets its own database URL and its own ports, and servers run from the worktree.
- Kill only processes you started, by PID. Never broad `pkill -f` patterns.
- Clean up everything you spawn: servers, headless browser profiles, temp files.

## 8. Subagents and budget

- Your own context is the most expensive resource. Hold the plan, the semantics, and the user's intent yourself, and delegate the volume.
- Strong models implement, cheap models explore. Batch several tasks into one agent instead of one agent per task, keep only a few in flight, and never fan out for a few small reads. Subagents do not spawn subagents.
- Verify subagent output against the source before relying on it or relaying it.
- Stop an agent that has run long with nothing to show. Do the work yourself when delegation costs more than it saves.

## 9. Documents and memory

- Durable facts live in the repo, not in a harness-specific memory, so they travel with the code. Record a ruling when told to, in the repo's memory files, one or two lines each, and prune entries that stop being true.
- Docs hold semantics, usage, edge cases, and invariants. No summary docs, no sweep docs, no narrative prose. Do not create a new doc when an existing one can absorb the content. Delete temp docs before commit.
- Do not save plans unless the user asks or the work is deferred.
- When context runs long, write a handoff doc before quality degrades: semantics, intent, decisions made, and remaining work, committed where the repo keeps them. Written for a reader with zero session context.

## 10. Harness notes

- Claude Code: make file changes with the Edit and Write tools, not shell one-liners, so the user sees the diffs. Check for repo skills and project agents before generic approaches.
- Any agent: prefer the repo's own build scripts, checks, and tooling over generic commands. If a check fails in a way the repo documents as baseline noise, say so instead of chasing it.

## 11. This project

- Public Deepwoken data for tools. Top-level data folders are tables, with one JSON file per row. `spec.json` describes their fields.
- Descriptions in `spec.json` are for external users. Explain what each field means without exposing filenames, repositories, storage syntax or other implementation details.
- For wording changes to `spec.json`, parse the JSON and compare it with the original to verify that only documentation strings changed.
