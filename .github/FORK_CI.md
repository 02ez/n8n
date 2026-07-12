# Fork-safe CI

This fork preserves build, unit-test, lint, typecheck, and packaging verification without depending on n8n organization infrastructure.

## Repository variables

The workflows are safe when these variables are unset. The recommended explicit values are:

```text
RUNNER_PROVIDER=github
ENABLE_INTERNAL_NOTIFICATIONS=false
ENABLE_AI_EVALS=false
ENABLE_IMAGE_PUBLISH=false
```

`RUNNER_PROVIDER=blacksmith` is the only value that selects Blacksmith runners. Any other value, including an unset variable, selects GitHub-hosted runners.

## Feature gates

| Variable | Default | Effect when enabled |
| --- | --- | --- |
| `ENABLE_INTERNAL_NOTIFICATIONS` | `false` | Allows the master CI workflow to send the private Slack failure notification. |
| `ENABLE_AI_EVALS` | `false` | Allows credential-backed AI evaluation workflows to run. |
| `ENABLE_IMAGE_PUBLISH` | `false` | Allows the private DHI/DockerHub/GHCR base-image workflow to run. |

Do not enable a gate until its repository or environment secrets have been intentionally provisioned.

## Image behavior

The benchmark image is built and loaded locally on the runner. It is not pushed to GHCR.

The n8n base image depends on an authenticated `dhi.io` base image. The entire base-image publishing job remains disabled unless `ENABLE_IMAGE_PUBLISH=true`.

## Pull-request checks

The fork-safe PR workflow runs:

- build and format check
- unit and integration test suites
- lint
- typecheck
- package dry-run validation
- aggregate required-check gate

Upstream internal E2E infrastructure, private metrics reporting, Slack notifications, private image publishing, and credential-backed AI evaluations are intentionally excluded.

## Observability

Fork-safe workflows emit a GitHub Actions summary and a log line in this form:

```text
CI_MODE=fork-safe runner=github internal_jobs=false ai_evals=false image_publish=false
```

## Rollback

Revert the merge commit that introduced the adaptation:

```bash
git revert <merge_commit>
```

No application data or schema rollback is required.
