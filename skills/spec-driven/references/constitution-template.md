# `.hermes.md` — constitution template

Copy into the project root as `.hermes.md` (or add the section to an existing one). Keep the
Constitution section short: it is the non-negotiable part. Whole file is capped at 20,000 chars.

---

# <Project> — Hermes project context

Hermes: when working in this repo, follow these rules.

## Constitution

Non-negotiable. A change that violates one is a stop-and-ask, not a workaround.

1. **<Rule stated as a testable obligation.>** — <one clause of why.>
2. **<Rule>** — <why.>
3. **<Rule>** — <why.>

## Build

- <e.g. run `make test` before declaring a change done>
- <e.g. publish order: source → build → render → verify live>

## Style

- <preferences that are NOT invariants — or move these into a skill>

---

## Worked example — Gridworks Guides (`~/gridworks/.hermes.md`)

1. **No number is published unless it was read off the current game build or an official
   developer announcement; if unverified, the page says so.** — the site's credibility is the product.
2. **Every guide that claims "played through and checked" was.** — a page compiled from sources
   carries a "how this was made" note saying it wasn't.
3. **Nothing goes live without Justin's word.** — draft and verify, then ask before publishing.
4. **Verify against the served bytes, not the local file.** — a 200 on disk is not a 200 on the site.
5. **Never commit secrets.** — `.env`, tokens, credentials, venvs are gitignored and never echoed.

Note what makes these work: each is checkable, each has a "why" that survives paraphrase, and
none of them is a preference. Contrast a bad constitution: "write accurate guides" (unfalsifiable),
"use consistent formatting" (a style preference — belongs in a skill).
