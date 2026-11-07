---
name: spec-driven
description: "Use when a plan needs a project constitution or converge."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [planning, governance, verification, workflow, spec-driven]
    related_skills: [plan, grill-me, test-driven-development, requesting-code-review]
---

# Spec-driven, the useful 20%

Two ideas borrowed from GitHub's Spec Kit — the **constitution** and the **converge** check —
without its CLI, its markdown explosion, or its static-spec drift. Use them on builds big enough
to need a written plan. Skip both for a one-file change or a bug fix.

## When to Use

- Starting or resuming a **multi-session build** that has a written plan.
- A project has invariants that must survive every session (fidelity rules, "never ship X",
  verify-before-publish).
- After implementing a plan, **before** declaring it done (converge).
- **Not** for a one-file change, a bug fix, or a throwaway prototype.

## 1. The constitution — invariants that can't be forgotten

Hermes auto-injects a project context file into the system prompt: **`.hermes.md` at the repo
root** (parent-walk to git root; first match wins; 20,000-char cap — see the project-context-files
reference inside the `hermes-agent` skill). It is injected when a session **starts** inside the
repo — CLI `hermes` run from that directory, or the desktop/TUI opened there. A session launched
elsewhere (e.g. a Telegram chat whose cwd is the home directory) does **not** get it
automatically, so the Enforcement rule below is the backstop. Keep the constitution in `.hermes.md`
anyway — it is the canonical, portable location (Claude Code, Codex, Cursor read the same file).

- **Location:** a `## Constitution` section at the top of the project's `.hermes.md` (create it if
  absent). Keep it under ~40 lines — it is the part that must never be violated, not a style guide.
- **Contents:** 5–10 numbered, **testable, non-negotiable** rules. Each is an obligation the agent
  can check, plus one clause of *why* (so it is applied, not cargo-culted).
  Good: "No number is published unless it was read off the build or an official announcement; if
  unverified, the page says so." Bad: "write good guides."
- **What does NOT belong:** formatting preferences, "be helpful", anything that can't be violated —
  those bloat the file and dilute the rules that matter. Preferences belong in a skill.
- **Enforcement:** read the constitution before planning any change; re-check it before declaring
  work done; a change that violates an article is a **stop-and-ask**, not a workaround. If a plan
  contradicts the constitution, the plan is wrong until the constitution is amended on purpose.

Template: `references/constitution-template.md`.

## 2. Converge — does the code actually match the plan?

Spec Kit's best later addition. Copy its one rule: **append-only, never rewrite.**

After implementing a plan (see the `plan` skill) and *before* saying it is done:

1. Re-read the plan and the constitution as the sole source of intent — not the code, not memory.
2. Assess the current code against each requirement/task and each constitution article.
3. Classify every discrepancy: **missing** (not built), **partial** (incomplete),
   **contradicts** (built against the intent or a constitution article — highest severity),
   **unrequested** (work the plan never called for — flag scope creep, don't silently keep it).
4. Append the gaps as new tasks at the end of the plan, each citing its source (`FR-3`, `Task 7`,
   `Constitution #2`). Do **not** renumber, do not edit the original tasks, do not touch code here.
5. Fix the appended tasks, then converge again. Repeat until clean.

If everything already matches, change nothing and say "converged." A converge that edits the plan
to match the code is not a converge — it is how drift gets certified.

## 3. What this deliberately leaves out

No CLI, no `.specify/` tree, no branch-per-spec, no 2,000-line spec for a 600-line feature. The
artifacts are the plan you already write (`plan` skill) plus the constitution you already have.
That is enough for the failure these prevent: **building the wrong thing confidently, and shipping
a plan the code quietly diverged from.**

## Pitfalls

- **Spec/constitution rot.** A constitution that no longer matches reality is a lie the agent will
  obey. Amend it when the project changes; review it when a plan contradicts it.
- **Ceremony for small work.** Spec Kit measured ~9× slower on small tasks. If the plan is one
  paragraph and the diff is one file, skip both halves.
- **Converge that rewrites.** The instant converge edits the plan to match the code, it stops being
  a check and becomes a rubber stamp.
- **Constitution as a wishlist.** Rules that can't be violated don't belong; they dilute the ones
  that can.
