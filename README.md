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
- **Standard README.md** — define what every project must document from day one
- **.env management** — standardize how environment variables are defined, documented, and managed
- **Version control** — versioning, tagging, and release practices
- **Centralizing shared YAML configs** — reuse CI, lint, and other configs across repos
- **Makefiles / Justfiles** — standardize common dev commands (install, build, test, deploy)
- **Project scaffolding** — bootstrap new projects with all standards pre-applied via TMG CLI
- **Nginx / SSL configuration via CLI** — automate server setup with a repeatable, defined approach
- **Docs** — document all standards and processes in our docs for easy reference and onboarding
- **Debugging** - define a clear process for debugging and troubleshooting issues in our projects without crutches
- **Package development** - fix issues with package linking and development workflow
- **Flexible docker-compose** - use variables in docker-compose to allow flexible configuration without needing to change .yaml files.

---

## Branch naming conventions

### Problem statement
- Current inconsistency: every project/team uses different conventions
- Work is performed on branches with varying names that complicates automation using cli tools

### Proposed standard:
- Base branches policy: 
  - `master` - every **application* and **package** must have master branch as the main development branch
  - `prod` - every **application** must have prod branch as the production branch
  - `dev` - optional, but if used, must be named dev and be the default branch for active development. Only for 
    **packages**, applications follow feature/release branching strategy and do not have dev branch

---

## .editorconfig

### Problem statement

- Inconsistent IDE settings lead to formatting issues, merge conflicts, and wasted time on tool configuration

### Proposed standard

Use a shared `.editorconfig` file across all projects to enforce consistent formatting rules:
- Enforce LF line endings, UTF-8 charset, 4-space indentation
- Override rules for specific file types if needed (e.g., 2-space indent for YAML)
- `ij_*` rules to ensure consistent settings in JetBrains IDEs (PhpStorm, WebStorm, etc.)
- Distributed via project scaffold or TMG CLI

---

## Linter / fixer strategy

### Problem statement

- Inconsistent linting rules across projects lead to code style drift and merge conflicts

### Proposed standard

- Define a shared set of linting rules for each stack (PHP, JS/TS) that all projects must adhere to
- Use centralized configs (e.g., shared package) to ensure consistency ans ease of installation/updates
- Automate linting and fixing as part of the development workflow:
  - Pre-commit hooks to run linters and auto-fix where possible

### Goal

- IDE does not show any linting **errors** and **warnings**
- IDE warning is considered a failure and must be fixed before commit
  - Unused variable but need to keep it → add comment to ignore the warning and tell the team that it's intentional
- Clean IDE setup of clean project should not require any manual configuration to avoid warnings and errors

---

## Standard README.md

### Problem statement

- Redundant documentation
- Outdated instructions
- Too much to read
- AI written: too much fluff, not enough actionable info

### Proposed standard

- Standardized README template with required sections and minimal content to get started:
  - Installation instructions
  - Local development setup
  - Environment variables setup instructions
  - Production deployment instructions (if applicable)
  - Release instructions (if applicable)
  - TODO section for future work and known issues
- `automd` to auto-generate parts from code itself
- Clear AI Skill about what to include in the README and how to keep minimal

### Goal

- Everybody can run installation and setup commands without asking a colleague or reading through a long document to set up the project locally

---

## .env management

### Problem statement

- Undocumented environment variables lead to confusion and setup issues
- Unknown env vars cause runtime errors that are hard to debug
- env vars relations and dependencies are not clear

### Proposed standard

2 ways to manage env vars:

- **CLI Tool** to manage env vars (Hard way)
  - `tmg env scaffold` - generate .env file from preconfigured schema (zod/yaml/toml)
  - `tmg env list` - list all env vars with descriptions and default values
  - `tmg env validate` - validate .env file against the schema and report missing/unknown vars

- **.env.example** as the single source of truth (Good for now way)
  - Every project must have a `.env.example` file with all **required** env vars, descriptions
  - Developers copy `.env.example` to `.env` and they are **ready to go**
  - *.env.example* should be maintained and updated as env vars change
  - *.env.example* should not be too verbose — only required vars with clear descriptions, **no optional vars** or 
    secrets
  - Ideally `.env` should be empty to run application locally
  - Application throws error in runtime/startup if required env var is missing or unknown env var is present
  - For production use a separate `.env.prod.example` file with production-specific vars and instructions to copy it
    to .env in production environment (if needed)

### Goal

- No more "what env vars do I need?" questions
- No invalid env vars causing runtime errors
- Clear documentation of env vars and their purpose

---

## Version control

### Release problem statement

- Unclear package release process
- Too much configuration in package itself (pipelines)

### Proposed standard

- Tool to bump package version (exists in js ecosystem)
  - `tmg-version-bump` - updates version in `composer.json`, adds git tag, creates chore commit, and optionally 
    pushes to remote
  - No huge release pipeline in the package itself

### Package version mismatch problem statement

- Developer does not know if package version was updated to call `composer update` that can lead to bugs, confusion, 
  wrong implementation
- Potentially wrong version semantics (patch, minor, major) 

### Proposed solution

- Use `composer validate --strict` to check `composer.lock` against `composer.json`
- Add this validate check before `docker compose up`, its `~10-50ms`

---

## Centralizing shared YAML configs

### Problem statement

- Each project has its own copy of CI/CD, lint, and other configs that are hard to maintain and update consistently
- Number of config files are too big, they add clutter and cognitive load while inspecting the project
- Many configs are generic and can be shared across projects

### Proposed standard

- Centralize shared configs in a single location (e.g., private package, dedicated config repo, or URL-based includes)
- Define which configs should be centralized (e.g., CI/CD pipelines, lint rules, Docker Compose snippets)

### Think about:
- Existence of some files in the project make IDE reconfigure, that we want to keep. Having files moved from root 
  may resolve in manual IDE configuration, unless we manage to configure IDE by ourselves via .editorconfig or some other way

### Examples of what can be centralized:

- .rr files sync between local and prod file
- kafka certificates are stored in every project and are updated on every docker compose up
- `functions.php` could be a shared package

### Goal

- As fewer files in the project as possible, only project-specific configs that cannot be shared

---

## Makefiles / Justfiles

### Problem statement

- While js has package.json scripts, php projects often lack a standardized way to define common scripts:
  - `bin` - some projects use bin directory for scripts
  - `Makefile` - some projects use Makefiles

### Proposed standard

- Standardize script definition for every project
- Centralize common scripts (kafka, nginx, database)
- Use `Justfile` instead of `Makefile`:
  - Pros:
    - Simpler syntax
    - Better error messages
    - No .PHONY configs
    - **Modules and includes** for better organization
    - User-friendly output: colorization, formatting, command grouping, command descriptions, TUI menus
    - No need to learn new syntax
    - Cool name: `just migrate`
  - Cons:
    - Requires installing `just` tool (using apt, for example)
      - We don't run Makefiles in CI/CD, so it's not a problem
      - We already provide installation via TMG CLI

### Example

Justfile usage:

![Justfile usage](/images/just-usage-example.png)

Justfile content:

![Justfile content](/images/just-config-example.png)

---

## Project scaffolding

### Problem statement

- Setting up a new project with all the necessary configs and standards is time-consuming and error-prone
- Usually we copy-paste from existing projects, which can lead to outdated configs and inconsistencies
- Copy-paste approach may lead to redundant files and configs that are not needed for the new project

### Proposed standard

- Use CLI tool to scaffold new projects with all standards and configs pre-applied (already possible):
  - Will replace variables in configs based on project type and user input (e.g., project name, stack)
  - Generate only necessary files based on project type
  - Base and most updated template is stored in one place
- Write AI Skill with instructions how to update existing project to match the scaffolded one, so we can easily keep 
  existing projects up to standard without manual work (already done for frontend)

---

## Nginx / SSL configuration via CLI

### Problem statement

- We rely on local nginx setup with some configuration that allows auto register nginx domains from /var/www/, but 
  it is not documented and conflicts with nginx that we can run with docker (port changing is required)
- We rely on dev certificates that should be presented in system, but it is not documented and is actually not 
  required if we have auto SSL, like we have on frontend (maybe harder to implement on backend).
- We use local domains for development, but their configuration is not documented. And why do we need domains when we can use ports?

### Proposed standard

- Use CLI Tool to automate nginx setup and SSL configuration:
  - `tmg setup backend` - sets up nginx config on local machine, configures it, configures hosts file, downloads 
    certificates.
  - Have a section in our DOCS about needed configuration and how to achieve it manually or with CLI tool

### Goal

- Developer sets up backend machine with one command
- Developer knows what is configured and how
- Developer understands errors related to nginx and SSL and can fix them without asking a colleague

---

## Docs

### Problem statement

- Standards and processes are not documented in a single place, leading to confusion and inconsistent practices
- New team members struggle to onboard and understand the expected practices without clear documentation

### Proposed standard

- Document all standards, processes, and best practices in a centralized location: https://docs.tmgbo.com/ (pages for backend already exists)

---

## Debugging and troubleshooting

### Problem statement

- Bugregator is not working, at least not working consistently, and we don't have a clear process for debugging and troubleshooting issues in our projects

### Proposed standard

> Should be researched

### Goal

- Clear process for debugging and troubleshooting issues in our projects without crutches

---

## Package development

### Problem statement

- Sometimes package development relies on application, and we don't have a way to link package to application 
  without publishing it to repository.

### Proposed solution

- Use some `link` mechanism to link package to application
- Research how Yii does it for some insights
- Define clear instructions when package can be linked and when it should be developed in a fully separate playground.

---

## Flexible docker-compose

### Problem statement

- We have some hardcoded values in docker-compose files that require manual changes when copy-pasting it to a new 
  project. 
- There is no clear place for dynamic variables
- There is no naming convention for containers

### Proposed solution

Use variables in docker-compose files to allow flexible configuration without needing to change .yaml files. For example:

```yaml
services:
  postgres:
    image: postgres:16
    container_name: ${APP_NAME:-app}-postgres
    restart: unless-stopped

    environment:
      POSTGRES_DB: ${DB_NAME:-app}
      POSTGRES_USER: ${DB_USER:-root}
      POSTGRES_PASSWORD: ${DB_PASSWORD:-root}

    ports:
      - "${DB_PORT:-5432}:5432"

    volumes:
      - pg_data:/var/lib/postgresql/data

    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${DB_USER:-root} -d ${DB_NAME:-app}"]
      interval: 5s
      timeout: 5s
      retries: 5

volumes:
  pg_data:
    name: ${APP_NAME}_pg_data
```

Justfile can ensure that `APP_NAME` is set int .env