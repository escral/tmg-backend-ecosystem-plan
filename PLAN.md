# Backend Ecosystem — Jira Issues Plan

> Based on the meeting summary and README. Each issue includes effort and impact estimates.
> Use this document to create Jira tickets and track implementation progress.

**Effort sizing:** XS (<2h) · S (half-day) · M (1 day) · L (2–3 days) · XL (4+ days)
**Impact:** High (affects all devs daily) · Medium (subset of workflows) · Low (nice to have)

---

## Decisions Log

| # | Decision |
|---|----------|
| 1 | Production branch is `prod` |
| 2 | Linting runs on pre-commit only — no CI lint checks |
| 3 | Psalm strict for all projects. BackOffice is an unwritten exception |
| 4 | No Rector for now |
| 5 | ENV CLI tool (`tmg env scaffold/validate`) is long-term — research ticket added to scope effort |
| 6 | Justfile is the standard task runner. Main file must be capitalized: `Justfile` |
| 7 | Nginx/SSL: documentation only for now. CLI automation is a separate future epic |

---

## EPIC 1 — Branch Naming & Git Conventions

| Key | Issue | Effort | Impact |
|-----|-------|--------|--------|
| 1.1 | Write and publish branch naming policy: `master` (all), `prod` (apps), `dev` optional (packages only) | XS | High |
| 1.2 | Add branch policy to docs.tmgbo.com | XS | High |
| 1.3 | Enforce branch naming via CLI guard or git hook | M | Medium |

---

## EPIC 2 — Editor Standardization (.editorconfig)

| Key | Issue | Effort | Impact |
|-----|-------|--------|--------|
| 2.1 | Create backend `.editorconfig` (PHP, YAML, Markdown, JSON + JetBrains `ij_*` rules) | S | High |
| 2.2 | Include `.editorconfig` in project scaffold template | XS | High |
| 2.3 | Distribute updated `.editorconfig` via `tmg` CLI update command | S | Medium |

---

## EPIC 3 — PHP Code Quality (Linting & Fixing)

| Key | Issue | Effort | Impact |
|-----|-------|--------|--------|
| 3.1 | Define shared PHP-CS-Fixer base ruleset (PSR baseline + framework extensions) | M | High |
| 3.2 | Extract PHP-CS-Fixer config into centralized shared package | M | High |
| 3.3 | Configure pre-commit hooks: run PHP-CS-Fixer, block commit on errors | M | High |
| 3.4 | Define shared Psalm config (strict) + baseline generation for new projects | L | High |
| 3.5 | Apply linting standards to one pilot project to validate the full workflow | M | High |

---

## EPIC 4 — README Standardization

| Key | Issue | Effort | Impact |
|-----|-------|--------|--------|
| 4.1 | Create standard README template (Installation, Dev Setup, ENV, Prod, Release, TODO) | S | High |
| 4.2 | Write AI Skill for README generation following the template | S | Medium |
| 4.3 | Research `automd` for auto-generating README sections from code | S | Low |
| 4.4 | Add README template to scaffold | XS | High |

---

## EPIC 5 — ENV Management

| Key | Issue | Effort | Impact |
|-----|-------|--------|--------|
| 5.1 | Define `.env.example` standard: required vars only, default values, clear descriptions | S | High |
| 5.2 | Add `.env.prod.example` convention for production-specific vars | XS | Medium |
| 5.3 | Define standard: application must throw on startup for missing or unknown env vars | M | High |
| 5.4 | Research CLI env tool: scope, cost, problems — `tmg env scaffold/validate` from schema | S | Medium |
| 5.5 | *(Long-term)* Implement CLI `tmg env scaffold` / `tmg env validate` from schema (Zod/YAML) | XL | Medium |

---

## EPIC 6 — Package Release Management

| Key | Issue | Effort | Impact |
|-----|-------|--------|--------|
| 6.1 | Implement `tmg version-bump` CLI command (bumps `composer.json`, git tag, chore commit, optional push) | L | High |
| 6.2 | Enforce `composer validate --strict` before `docker compose up` via Justfile | S | High |
| 6.3 | Policy: `composer.lock` must always be committed — document and enforce | XS | High |
| 6.4 | Define and document semantic versioning policy (patch/minor/major criteria) | S | Medium |
| 6.5 | Document complete release process end-to-end in docs.tmgbo.com | S | High |

---

## EPIC 7 — Centralized YAML Configs

| Key | Issue                                                                              | Effort | Impact |
|-----|------------------------------------------------------------------------------------|--------|--------|
| 7.1 | Audit all projects: identify repeated YAML configs (CI, RoadRunner, Kafka, etc.)   | M | Medium |
| 7.2 | Extract shared RoadRunner config to centralized source                             | M | Medium |
| 7.3 | Solve Kafka certificates duplication (central storage / auto-sync on `compose up`) | L | Low |
| 7.4 | Evaluate CI/CD pipeline config sharing (Bitbucket includes or shared templates)    | M | Medium |

---

## EPIC 8 — Task Runner (Justfile)

> Standard is `Justfile` (capitalized). No Makefiles in new projects.

| Key | Issue | Effort | Impact |
|-----|-------|--------|--------|
| 8.1 | Prototype `Justfile` for one project (migrate, up, test, lint standard targets) | S | High |
| 8.2 | Create `Justfile` module library (common recipes: docker, db, kafka, nginx) | L | High |
| 8.3 | Add `just` installation to `tmg` CLI setup command | XS | High |
| 8.4 | Add standard `Justfile` to scaffold template | XS | High |
| 8.5 | Migrate existing projects from Makefile/bin scripts to `Justfile` | L | Medium |
| 8.6 | Add shell alias: `just` falls back to `make` when `Justfile` is missing but `Makefile` exists — smoother migration, distribute via `tmg` CLI setup | S | Medium |

---

## EPIC 9 — Project Scaffolding

| Key | Issue | Effort | Impact |
|-----|-------|--------|--------|
| 9.1 | Create backend project template repository | L | High |
| 9.2 | Point `tmg scaffold backend` to the backend template repository (already implemented) | XS | High |
| 9.3 | Write AI Skill for updating existing projects to match latest template | M | Medium |

---

## EPIC 10 — Docker & Local Setup Consistency

| Key | Issue | Effort | Impact |
|-----|-------|--------|--------|
| 10.1 | Define flexible `docker-compose` variable standard (APP_NAME, DB_NAME, ports, container names) | S | High |
| 10.2 | Define container naming convention (e.g. `${APP_NAME}-postgres`) | XS | Medium |
| 10.3 | Add standard flexible `docker-compose.yml` to scaffold template | XS | High |
| 10.4 | Migrate existing project docker-compose files to the flexible variable approach | L | Medium |
| 10.5 | Create docker-compose snippet library (postgres, redis, kafka, nginx, etc.) as a reference to copy from | M | High |
| 10.6 | Write AI Skill for working with the docker-compose snippet library (find, copy, adapt snippets) | S | Medium |

---

## EPIC 11 — Nginx/SSL Setup

> Documentation only for now. CLI automation (`tmg setup backend`) is a separate future epic.

| Key | Issue | Effort | Impact |
|-----|-------|--------|--------|
| 11.1 | Document nginx/SSL setup: manual steps, ports vs domains decision, certificate setup | S | High |
| 11.2 | Add nginx/SSL section to docs.tmgbo.com backend onboarding | XS | High |

---

## EPIC 12 — Debugging & Monitoring

| Key | Issue | Effort | Impact |
|-----|-------|--------|--------|
| 12.1 | Audit Bugregator: identify root cause of failures, evaluate fix vs replace | M | High |
| 12.2 | Research and propose debugging stack (Bugregator, Xdebug, alternatives) | M | High |
| 12.3 | Define and document standard debugging process for all projects | S | High |

---

## EPIC 13 — Package Development Workflow

| Key | Issue | Effort | Impact |
|-----|-------|--------|--------|
| 13.1 | Research local package linking mechanism (Composer path repos, symlinks, Yii approach) | S | Medium |
| 13.2 | Define and document when to link vs develop in isolated playground | S | Medium |

---

## EPIC 14 — Documentation (docs.tmgbo.com)

| Key | Issue | Effort | Impact |
|-----|-------|--------|--------|
| 14.1 | Document all decisions from this meeting as individual standard pages | M | High |
| 14.2 | Create onboarding guide for new backend developers | M | High |

---

## EPIC 15 — Kafka Automatic Channel Creation

| Key | Issue | Effort | Impact |
|-----|-------|--------|--------|
| 15.1 | Research Kafka topic auto-creation approaches (init container, admin client on startup, CLI command) | S | Medium |
| 15.2 | Define where topic definitions live per project (config file, schema, Justfile recipe) | S | Medium |
| 15.3 | Implement Kafka topic auto-creation for local `docker-compose` setup | M | High |
| 15.4 | Document Kafka topic management standards (naming, creation, local vs prod) | S | Medium |

---

## Priority Roadmap

---

### Now — Epics 14, 11, 2, 4, 1, 6, 10

Unblock the dev environment and establish foundational standards. Low effort, high immediate value.

- **EPIC 14** — Documentation — migrate all decisions out of this repo into docs.tmgbo.com; backend onboarding guide *(this repo is temporary)*
- **EPIC 11** — Nginx/SSL Setup *(blocking: local backend environment does not work)*
- **EPIC 2** — Editor Standardization — create and distribute `.editorconfig`
- **EPIC 4** — README Standardization — template + AI Skill
- **EPIC 1** — Branch Naming & Git Conventions — policy document + docs page
- **EPIC 6** — Package Release Management — `composer.lock` policy, semver docs, `composer validate --strict`
- **EPIC 10** — Docker & Local Setup — flexible `docker-compose` standard, naming convention, scaffold template

---

### Short-term — Epics 3, 5, 6, 8, 15

Core quality tooling and daily workflow improvements. Medium effort, high daily impact.

- **EPIC 3** — PHP Code Quality — PHP-CS-Fixer shared config, Psalm strict setup, pre-commit hooks, pilot project
- **EPIC 5** — ENV Management — `.env.example` standard, `.env.prod.example`, startup validation, CLI tool research
- **EPIC 6** — Package Release Management — `tmg version-bump` CLI command, release process docs
- **EPIC 8** — Task Runner — `Justfile` prototype, module library, `just` installation via CLI, `make` fallback alias
- **EPIC 15** — Kafka Automatic Channel Creation — research, local setup, documentation

---

### Mid-term — Epics 9, 7, 12, 13

Depends on standards from Now/Short-term being in place first.

- **EPIC 9** — Project Scaffolding — backend template repo, `tmg scaffold backend` repo link, AI Skill
- **EPIC 7** — Centralized YAML Configs — audit, extract RoadRunner config, evaluate CI/CD sharing
- **EPIC 12** — Debugging & Monitoring — Bugregator audit, stack research, process documentation
- **EPIC 13** — Package Development Workflow — local linking research, dev workflow documentation

---

### Long-term — Epics 5, 8, 10

Research-heavy or requires significant CLI work. No blockers on other teams.

- **EPIC 5** — ENV CLI Tool — implement `tmg env scaffold` / `tmg env validate` from schema
- **EPIC 8** — Migrate all existing projects from Makefile/bin scripts to `Justfile`
- **EPIC 10** — Migrate all existing docker-compose files to flexible variable approach + snippet library
