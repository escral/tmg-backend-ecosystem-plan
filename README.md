# Better Backend Ecosystem

## The goal of the meeting is to review

- What standards/tools we already have
- What we want as a baseline across projects
- How configuration should work
- How we enforce standards without friction
- How TMG CLI can make setup & maintenance trivial

## Topics

- **Branch naming conventions** — agree on a shared Git branching standard across all projects
- **.editorconfig** — enforce consistent formatting at the editor level, regardless of IDE
- **Linter / fixer strategy** — shared lint rules and auto-fix pipelines per stack
- **Standard README.md / .env.example** — define what every project must document from day one
- **Version control** — versioning, tagging, and release practices
- **Centralizing shared YAML configs** — reuse CI, lint, and other configs across repos
- **Makefiles / Justfiles** — standardize common dev commands (install, build, test, deploy)
- **Project scaffolding** — bootstrap new projects with all standards pre-applied via TMG CLI
- **Nginx / SSL configuration via CLI** — automate server setup with a repeatable, defined approach