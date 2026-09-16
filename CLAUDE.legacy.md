# CLAUDE.md — Legacy repository operating rules template

A starting `CLAUDE.md` for running Claude Code on a long-lived application
(`ASP.NET MVC 5` / `.NET Framework 4.x` in the examples, but the rules are
stack-agnostic) where the goal is safe change rather than modernisation.
Companion to the article "Legacy Code Is Not a Rewrite Problem":
<MEDIUM-URL>

Copy this to the root of the repository, fill in the "Project" section, and
tune the numbers. The model routing section from the earlier template
("One Model for Everything Is a Mistake") can sit underneath this one; the
operating rules take precedence where they conflict.

---

## Project

<!-- Replace with your own. Keep it short. -->

- Name: <project name>
- Stack: <e.g. ASP.NET MVC 5, .NET Framework 4.8, EF6 + ADO.NET, SQL Server 2016, jQuery, Telerik UI>
- Age: <first commit year> — assume every odd-looking thing is load-bearing
- Layout:
  - `Controllers/`, `Views/`, `Models/`   — MVC, conventional
  - `DAL/`                               — mixed EF6 and ADO.NET; do not unify
  - `Scripts/`                           — hand-written jQuery; some views depend on element IDs
  - `Database/Procs/`                    — stored procedures, source of truth for the DB
- Build: <e.g. `msbuild /p:Configuration=Release`>
- Tests: <e.g. `tests/Characterisation/` — run with `vstest.console`> (may be "none yet")
- Deploy: <manual/scripted; note the checklist location>

---

## Operating rules for this repository

This is a production application. It has very few automated tests. The
priority is SAFE CHANGE, not modernisation. Read this section before every
task.

### 1. Understand before touching
- Before editing, trace every caller of the code you intend to change.
  Views, partials, JavaScript, stored procedures, scheduled jobs, and
  reports count as callers.
- Report what you found as a short list. If a caller is unclear, STOP and
  ask.

### 2. Pin the behaviour first
- Every change starts by locking in current behaviour: a characterisation
  test where the code can be exercised in isolation, otherwise a written
  manual test plan in `docs/test-plans/<feature>.md` that the user can run
  before and after.
- "Current behaviour" includes the bugs. Do not fix a bug you were not
  asked to fix; report it under rule 5.

### 3. Blast radius
- A single task touches at most 5 files unless the brief says otherwise.
  Exceeding it is a signal to stop and re-plan, not a problem to push
  through.
- Match the existing style of the file you are in, even where it is dated.
  Consistency inside a file beats consistency with the current year.
- Do not introduce `async`/`await`, dependency injection, a repository, a
  new base class, or a new package as part of another change. Each of
  those is its own task with its own brief.
- New stored procedure parameters are nullable with a default so existing
  callers are unaffected.

### 4. Never without explicit approval
- Upgrading the framework, any package, or the language version
- Editing `Web.config`, `App.config`, or any config transform
- Changing the data access pattern in a given file
- Renaming, moving, or deleting anything public: controllers, actions,
  routes, view names, model properties bound by views or JavaScript,
  element IDs referenced from scripts
- Changing a database schema or a stored procedure signature
- Touching authentication, authorisation, or anything under `Security/`

### 5. Report, do not improve
- Anything you notice that "should" be fixed goes in a section at the end
  of your reply titled **Observed, not changed**. One line each, with the
  file and line.
- Do not act on that list unless asked. The user maintains it as the
  technical debt register.

### 6. Escalation format
When the code contradicts the request, or the request would violate a rule
above, stop and reply with:

```
Blocked by:   <rule or contradiction>
What I found: <two or three lines>
Options:      <A / B, with the cost and risk of each>
```

Do not proceed on an assumption.

---

## Brief format for legacy changes

```
Task:        <one sentence>
May touch:   <explicit file list, max 5>
Procs:       <stored procedures in scope, if any>
Style:       match <reference file>
Must not:    new abstractions, new packages, async, config, renames
Test plan:   <path to characterisation test or manual plan>
Done when:   <observable outcome; test plan passes before and after>
Report back: files changed; "Observed, not changed" list
```

---

## Review checklist (after every task)

- [ ] Only the listed files were touched
- [ ] Style matches the surrounding code
- [ ] No new abstractions, packages, async, or config changes
- [ ] New proc parameters are nullable with defaults
- [ ] Test plan run before and after, results recorded
- [ ] "Observed, not changed" items copied to the debt register

---

## Notes for humans

- The five-file limit will stop you as often as it stops the model. That
  is the point.
- When an "Observed, not changed" item is finally scheduled, it gets its
  own brief and its own five files.
- If you find yourself granting rule 4 approvals daily, the application
  is ready for a modernisation project with its own plan. Do not let it
  happen one grid column at a time.
