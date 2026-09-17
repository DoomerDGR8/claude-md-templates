# claude-md-templates

Drop-in `CLAUDE.md` templates for [Claude Code](https://docs.claude.com/en/docs/claude-code), written from 15+ years of building and maintaining enterprise `.NET` systems. Each template is the companion to a Medium article that explains the reasoning behind it.

`CLAUDE.md` is the file Claude Code reads at the start of every session in a repository. Most people use it for build commands and coding conventions. These templates use it for something else: telling the model who decides what, and what it must not do without asking.

## Templates

| File | Use it when | Article |
|---|---|---|
| [`CLAUDE.routing.md`](CLAUDE.routing.md) | Starting a new project and you want work split across the Claude model family by the kind of decision, not by how hard the task feels. Architecture at the top tier, mechanical work at the bottom, with escalation rules between them. | [One Model for Everything Is a Mistake](https://doomerdgr8.medium.com/one-model-for-everything-is-a-mistake-64d3a7e3788e) |
| [`CLAUDE.legacy.md`](CLAUDE.legacy.md) | Running Claude Code on a long-lived production application with few tests, where the goal is safe change rather than modernisation. Trace before touching, pin behaviour first, a five-file blast radius, and a "report, do not improve" rule. | Legacy Code Is Not a Rewrite Problem *(link to follow)* |

The examples use `ASP.NET` and `SQL Server` because that is what I work with. The rules are stack-agnostic.

## How to use one

1. Copy the template to the root of your repository as `CLAUDE.md`.
2. Fill in the **Project** section at the top. Keep it short; it is context, not documentation.
3. Tune the numbers and the "never without approval" list to your codebase.
4. Read the **Notes for humans** section at the bottom. The rules apply to you as much as to the model.

The two templates can be combined. On a legacy repository, put the operating rules from `CLAUDE.legacy.md` first and the routing policy from `CLAUDE.routing.md` underneath; the operating rules win where they conflict.

## Principles behind both

- **The expensive model should make fewer, bigger decisions.** If the top tier is editing files all over the repository, the brief was wrong.
- **"Do not" is half of every good brief.** A model with no boundaries will make architectural decisions at whatever tier it happens to be running on.
- **Agents that stop are worth more than agents that finish.** Every template has an escalation format. Use it.
- **The policy outlives the models.** When a model in the family is renamed or retired, change the tier heading, not the rules.

## Contributing

Issues and pull requests are welcome, especially the rule you had to add after something went wrong. Please keep templates under two screens; anything longer belongs in a linked document.

## Licence

[MIT](LICENSE). Copy them, change them, ship them.
