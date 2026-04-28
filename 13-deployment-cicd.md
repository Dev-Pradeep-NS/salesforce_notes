# 13 - Deployment and CI/CD (Detailed)

## Learning goals
- Understand release flow from dev to production.
- Use Salesforce CLI for deploy/test validation.
- Design a practical CI/CD pipeline for Salesforce metadata.

## Typical deployment lifecycle
1. Develop in scratch org or dev sandbox.
2. Commit metadata/source to git.
3. Run static checks and tests in CI.
4. Validate deployment on target org (check-only style).
5. UAT sign-off.
6. Production deployment and post-deploy verification.

## Tools in workflow
- VS Code + Salesforce extensions
- Salesforce CLI (`sf`)
- Workbench (utility tasks)
- Git platform CI/CD (GitLab/GitHub)
- Change Sets (org-to-org metadata deployment in many enterprise setups)
- Developer Console (debugging, logs, execute anonymous, quick tests)

## Environment types
- **Developer org / Trailhead Playground:** learning and experiments.
- **Scratch org:** temporary source-driven development environment.
- **Developer sandbox:** isolated development.
- **Partial Copy sandbox:** testing with a sample of production data.
- **Full sandbox:** production-like testing with full data copy.
- **Production:** live business org.

Exam pattern:
- Use Full or Partial Copy when realistic production data volume or data shape matters.
- Use Developer or scratch orgs for isolated development.

## CLI command examples
```bash
sf org login web --alias dev-org
sf project deploy start --target-org dev-org
sf apex run test --target-org dev-org --code-coverage --result-format human
```
What this snippet does:
- Authenticates to an org, deploys project metadata, then runs Apex tests with coverage output.
- Represents a minimal local validate cycle before PR or CI handoff.

Validation against target:
```bash
sf project deploy validate --target-org uat-org
```
What this snippet does:
- Performs a check-only style validation against target org without committing deployment changes.

## Recommended pipeline stages
- **Lint/static checks:** metadata quality and formatting.
- **Unit tests:** fast feedback on PR.
- **Validate deploy:** no-prod-impact dry run.
- **Deploy stage:** controlled deployment on protected branch/tag.
- **Post-deploy checks:** smoke tests + monitoring.

## CI/CD design guidelines
- Keep pull requests small and reviewable.
- Require test pass and reviewer approval before merge.
- Use branch protections.
- Keep rollback plan documented for each release.

## Debug logs and monitoring
Debugging tools to know:
- Debug logs
- Log levels
- Developer Console
- Salesforce CLI test output
- Apex Replay Debugger
- Setup pages for Apex Jobs, Scheduled Jobs, Paused Flow Interviews, and deployment status

Debug log categories include Apex Code, Apex Profiling, Database, Validation, Workflow, and System. Increase log levels only when needed because logs can become noisy quickly.

## Metadata vs business data deployment
- **Metadata:** configuration and code, such as Apex classes, objects, fields, flows, layouts, permission sets.
- **Business data:** records, such as Accounts, Contacts, Opportunities, Cases.

Deployment tools move metadata. Data tools such as Data Loader, Data Import Wizard, or Bulk API move records.

Do not confuse a metadata deployment with a data migration.

## GitLab pipeline sketch (concept)
- `validate` job on merge requests
- `deploy-uat` on `develop`
- `deploy-prod` on tagged release with approval

## Common mistakes
- Deploying without meaningful tests.
- Large "big bang" releases with mixed risky changes.
- No backout strategy.
- Ignoring metadata dependencies between components.

## Change Sets (practical note)
- Commonly used in sandbox -> production style deployments.
- Good for admin-led or mixed admin/dev release workflows.
- Limitations:
  - manual packaging effort
  - weaker version-control traceability than source-driven CI/CD
- Recommendation: learn both Change Sets and source-driven pipelines, since many orgs use both.

## Practice set
1. Define a two-stage pipeline (`validate`, `deploy`) for a sandbox org.
2. Add test reporting artifact and fail build on critical test failures.
3. Create a one-page release runbook template (pre-checks, deploy steps, rollback).
4. Choose the right sandbox type for development, QA, performance testing, and UAT scenarios.
