# CLAUDE.md — Tiered model routing template

A starting `CLAUDE.md` for Claude Code projects that splits work across the
Claude model family by the kind of decision being made, not by how hard the
task feels. Companion to the article "One Model for Everything Is a Mistake":
https://doomerdgr8.medium.com/one-model-for-everything-is-a-mistake-64d3a7e3788e

Copy this file to the root of your repository, fill in the "Project" section,
and adjust the tier names as the model family changes. The policy outlives
the models.

---

## Project

<!-- Replace with your own. Keep it short; this is context, not documentation. -->

- Name: <project name>
- Stack: <e.g. .NET 8 Web API, EF Core, SQL Server, xUnit>
- Layout:
  - `src/Api/`            — ASP.NET Core controllers, DI wiring
  - `src/Application/`    — use cases, DTOs, validation, pipeline behaviours
  - `src/Domain/`         — entities, value objects, domain rules
  - `src/Infrastructure/` — EF Core, external services
  - `tests/`              — one test project per `src` project
- Build: `dotnet build`
- Test: `dotnet test`
- Conventions: <link to CONTRIBUTING.md or list the three rules that matter>

---

## Model routing policy

This project uses tiered delegation. The orchestrating session runs
**Fable 5 on low effort**. It thinks in decisions, not in files.

### Tier 1 — Architecture (Fable 5, low)

Owns: solution structure, project boundaries, dependency direction,
persistence strategy, cross-cutting concerns (auth, logging, auditing,
caching), and third-party library choices.

Does NOT write feature code.

Working pattern:
1. Propose the design in prose: what goes where, why, and the two or three
   decisions that would be painful to reverse.
2. Ask the user to confirm.
3. Delegate to the tiers below with a written brief (see "Brief format").
4. Review the returned work against the design and this file. Report
   violations; do not silently fix them.

### Tier 2 — Major features (Opus sub-agents, low → high)

Any change that spans more than one project, requires design judgement, or
introduces a new abstraction.

Effort selection:
- `low`    — specification is complete and unambiguous
- `medium` — specification is clear but has open implementation choices
- `high`   — specification is vague, or the feature touches security, money,
             or data integrity

Every Tier 2 brief must name the projects the agent may touch. Everything
else is read-only.

### Tier 3 — Straightforward implementation (Sonnet 5, low → high)

A clear specification AND an existing pattern in this repository to copy.
"Build another one of these, in the same shape."

Default effort: `low`. Raise to `medium` only if the reference pattern has
to be adapted rather than mirrored.

Every Tier 3 brief must name the reference file(s) to copy from and must
include the sentence "Do not introduce any new abstractions."

### Tier 4 — Mechanical tasks (Haiku)

Renames, boilerplate, XML documentation comments, formatting, tests that
lock in existing behaviour, dependency bumps with no API changes.

No logic changes. Run several in parallel where the tasks are independent.

---

## Escalation rules (push up)

An agent at any tier STOPS and reports to the tier above when:

- it needs a new abstraction, interface, or base class that does not exist;
- it wants to touch a project not named in its brief;
- it discovers the specification contradicts the existing code;
- it would need to add or upgrade a NuGet/npm/pip package;
- it is about to change a public API surface or a database schema.

"Report" means: state what was attempted, what blocked it, and a proposed
option. Do not proceed on assumption.

## De-escalation rules (push down)

- If Tier 1 finds itself editing more than two files, the brief was wrong.
  Stop, rewrite the brief, and delegate.
- If a Tier 2 agent finishes and the diff is a copy of an existing pattern,
  the task should have gone to Tier 3. Note it for next time.
- If a Tier 3 agent's task turns out to be pure renaming or boilerplate,
  hand the remainder to Tier 4.

---

## Brief format

Every delegation, at every tier, uses this shape:

```
Task:        <one sentence>
Tier/effort: <e.g. Opus, medium>
May touch:   <list of projects or paths>
Reference:   <files to copy from, if Tier 3>
Must not:    <explicit exclusions — new abstractions, new packages, schema>
Done when:   <observable outcome — tests pass, endpoint responds, etc.>
Report back: files changed; anything where the architecture got in the way
```

The last line is not optional. "Where the architecture got in the way" is
the feedback loop that tells the architecture tier its boundaries are wrong
before the code does.

---

## Review checklist (Tier 1, after every delegation)

- [ ] Only the named projects were touched
- [ ] No new abstractions without approval
- [ ] No new packages without approval
- [ ] Tests exist for new behaviour and pass
- [ ] Every "architecture got in the way" note has been read and either
      acted on or explicitly dismissed

---

## Notes for humans

- This file is read at the start of every Claude Code session. Keep it under
  two screens; anything longer belongs in `docs/`.
- When a model in the family is renamed or retired, change the tier heading,
  not the policy.
- If you find yourself overriding the routing by hand more than once a day,
  the policy is wrong for this project. Fix the file, not the habit.
