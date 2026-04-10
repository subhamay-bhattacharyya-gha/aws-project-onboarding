# AWS Project Onboarding — Reusable Workflow

![Built with Claude Code](https://img.shields.io/badge/Built_with-Claude_Code-blueviolet?logo=anthropic&logoColor=white)&nbsp;![Terraform](https://img.shields.io/badge/Terraform-7B42BC?logo=terraform&logoColor=white)&nbsp;![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?logo=githubactions&logoColor=white)&nbsp;![Release](https://github.com/subhamay-bhattacharyya-gha/aws-project-onboarding/actions/workflows/release.yaml/badge.svg)&nbsp;![Commit Activity](https://img.shields.io/github/commit-activity/t/subhamay-bhattacharyya-gha/aws-project-onboarding)&nbsp;![Last Commit](https://img.shields.io/github/last-commit/subhamay-bhattacharyya-gha/aws-project-onboarding)&nbsp;![Release Date](https://img.shields.io/github/release-date/subhamay-bhattacharyya-gha/aws-project-onboarding)&nbsp;![Repo Size](https://img.shields.io/github/repo-size/subhamay-bhattacharyya-gha/aws-project-onboarding)&nbsp;![File Count](https://img.shields.io/github/directory-file-count/subhamay-bhattacharyya-gha/aws-project-onboarding)&nbsp;![Issues](https://img.shields.io/github/issues/subhamay-bhattacharyya-gha/aws-project-onboarding)&nbsp;![Top Language](https://img.shields.io/github/languages/top/subhamay-bhattacharyya-gha/aws-project-onboarding)&nbsp;![Custom Endpoint](https://img.shields.io/endpoint?url=https://gist.githubusercontent.com/bsubhamay/8258f1e702044c00c3d090add48c0f51/raw/aws-project-onboarding.json?)

A centralised, reusable GitHub Actions workflow (`workflow_call`) that lets any application repo in the organisation onboard itself to HCP Terraform, GitHub Environments, and repository rulesets with a single `env.json` file.

## Features

- **Single config file** — all project settings live in `env.json` at the repo root
- **HCP Terraform integration** — creates workspaces, links VCS, and seeds variables
- **GitHub Environments** — creates environments with `AWS_ACCOUNT_ID` and `AWS_REGION` per environment; `SNOWFLAKE_ACCOUNT_NAME` and `SNOWFLAKE_ORGANIZATION_NAME` are created only for Snowflake-related repositories
- **Production approval gate** — configurable reviewers and wait timer (exact match only)
- **Repository rulesets** — branch protection with PR reviews, status checks, force-push blocking
- **Fully idempotent** — every step is safe to re-run on an already-onboarded repo
- **Reusable design** — consumed via `workflow_call` by any repo in the org

## How it works

The workflow runs 9 sequential steps:

| Step | Name                   | Description                                         |
| ---- | ---------------------- | --------------------------------------------------- |
| 0    | `parse`                | Validate and extract all fields from `env.json`     |
| 1    | `check`                | Check if the HCP Terraform workspace already exists |
| 2    | `create`               | Create workspace (skipped if it exists)             |
| 3    | `resolve`              | Merge workspace ID from create or check             |
| 4    | `link-vcs`             | Attach VCS repository to the workspace              |
| 5    | `set-vars`             | Seed workspace variables                            |
| 6    | `setup-environments`   | Create GitHub Environments + set AWS / Snowflake variables |
| 7    | `setup-ruleset`        | Create or update repository ruleset                 |
| 8    | `summary`              | Write `$GITHUB_STEP_SUMMARY`                        |

## Quick start

### 1. Add `env.json` to your application repo root

Copy `env.json` from this platform repo and fill in your values:

```json
{
  "hcp": {
    "organization": "my-hcp-org",
    "workspace_name": "my-app",
    "terraform_version": "1.7.0",
    "execution_mode": "remote",
    "branch": "main",
    "auto_apply": false,
    "workspace_variables": [
      { "key": "TF_VAR_region", "value": "europe-west2", "sensitive": false, "category": "terraform" }
    ]
  },
  "environments": [
    { "name": "devl", "aws_account_id": "111111111111", "aws_region": "eu-west-1", "snowflake_account_name": "DOC83156", "snowflake_organization_name": "AVDNPDD" },
    { "name": "test", "aws_account_id": "222222222222", "aws_region": "eu-west-1", "snowflake_account_name": "DOC83158", "snowflake_organization_name": "AVDNPDE" },
    { "name": "prod", "aws_account_id": "333333333333", "aws_region": "eu-west-1", "snowflake_account_name": "DOC83157", "snowflake_organization_name": "AVDNPDF" }
  ],
  "github": {
    "production_environment": "prod",
    "reviewer_teams": ["platform-team"],
    "wait_timer": 5
  },
  "ruleset": {
    "enabled": true,
    "target_branch": "~DEFAULT_BRANCH",
    "require_pr": true,
    "required_approvals": 1,
    "dismiss_stale_reviews": true,
    "required_status_checks": ["ci / build", "ci / test"],
    "block_force_pushes": true,
    "prevent_deletion": true
  }
}
```

### 2. Add the caller workflow

Copy `templates/onboard-aws-project.yaml` to `.github/workflows/onboard-aws-project.yaml` in your app repo:

```yaml
name: Onboard GCP Project

on:
  push:
    branches: [main]
    paths:
      - 'env.json'
      - '.github/workflows/onboard-aws-project.yaml'
  workflow_dispatch:

jobs:
  onboard:
    uses: <ORG>/aws-project-onboarding/.github/workflows/aws-project-onboarding.yaml@v1
    secrets: inherit
```

### 3. Configure org-level secrets

| Secret                   | Description                                                      |
| ------------------------ | ---------------------------------------------------------------- |
| `TFE_TOKEN`              | HCP Terraform API token                                          |
| `TF_VCS_OAUTH_TOKEN_ID`  | OAuth token ID for VCS provider                                  |
| `GITHUB_PAT`             | Fine-grained PAT (environments: read/write, administration: write) |

### 4. Push and go

Push `env.json` and the caller workflow to `main`. The onboarding runs automatically.

## Updating `env.json`

`env.json` is the single source of truth for the onboarding. It is passed verbatim as the `config` input of the reusable workflow — every field below maps to a step output in the `parse` step and then to a downstream API call. Edit the file, commit, push, and the onboarding runs automatically because the caller workflow is triggered on pushes that touch `env.json`.

### `hcp` block — HCP Terraform workspace

| Field | Required | Default | Notes |
| --- | --- | --- | --- |
| `hcp_org` | yes | — | HCP Terraform organisation that owns the workspace |
| `workspace_name` | no | repo name | Defaults to `${{ github.event.repository.name }}` |
| `terraform_version` | no | `1.7.0` | Any version supported by HCP Terraform |
| `execution_mode` | no | `remote` | `remote`, `local`, or `agent` |
| `working_directory` | no | `""` | Subdirectory inside the VCS repo where Terraform runs |
| `branch` | no | `main` | VCS branch the workspace tracks |
| `auto_apply` | no | `false` | Auto-apply successful plans |
| `tfe_address` | no | `https://app.terraform.io` | Override for TFE / custom HCP endpoints |
| `workspace_variables` | no | `[]` | Array of `{key, value, sensitive, category}` objects |

### `environments` block — GitHub Environments

An array of per-environment objects. At least one entry is required. The workflow always injects a `ci` environment cloned from `devl` if one exists, so you do not need to list `ci` yourself.

| Field | Notes |
| --- | --- |
| `name` | Environment name (`devl`, `test`, `prod`, …). The workflow iterates in the order listed |
| `aws_account_id` | Populates the `AWS_ACCOUNT_ID` variable **and** is used to derive `AWS_OIDC_ROLE` as `arn:aws:iam::<id>:role/github-oidc-role` |
| `aws_region` | Populates the `AWS_REGION` variable |
| `snowflake_account_name` | Optional. Populates `SNOWFLAKE_ACCOUNT_NAME` (skipped if empty) |
| `snowflake_organization_name` | Optional. Populates `SNOWFLAKE_ORGANIZATION_NAME` (skipped if empty) |

### `github` block — environment protection

| Field | Default | Notes |
| --- | --- | --- |
| `production_environment` | `prod` | Exact-match name of the production environment |
| `approval_environments` | `["test", <production_environment>]` | Exact-match list of environments that receive required reviewers, `prevent_self_review: true`, and a main-only deployment branch policy |
| `reviewer_teams` | `[]` | Team slugs (not names). The workflow resolves each slug to a team ID, grants the team `push` access to the repo, then attaches it as a required reviewer on every protected environment |
| `wait_timer` | `0` | Minutes to wait before allowing a protected deployment to proceed |

> **Why teams are auto-granted push access.** GitHub silently drops reviewer teams that have no repository access, which then makes `prevent_self_review` fail with *"Required reviewers must have at least one reviewer"*. The workflow upserts team repo access via `PUT /orgs/{org}/teams/{slug}/repos/{owner}/{repo}` before using the team as a reviewer.

### `ruleset` block — branch protection

| Field | Default | Notes |
| --- | --- | --- |
| `ruleset_enabled` | `true` | When `false`, the `setup-ruleset` step is skipped entirely |
| `target_branch` | `~DEFAULT_BRANCH` | Conditions include pattern for the `standard-branch-protection` ruleset |
| `require_pr` | `true` | Adds the `pull_request` rule |
| `required_approvals` | `1` | Minimum approving reviews |
| `dismiss_stale_reviews` | `true` | Dismiss stale reviews on new pushes |
| `required_status_checks` | `[]` | Array of check contexts. Rule is omitted when empty |
| `block_force_pushes` | `true` | Adds the `non_fast_forward` rule |
| `prevent_deletion` | `true` | Adds the `deletion` rule |

A second ruleset, `branch-name-policy`, is always created targeting `~ALL` (except `refs/heads/main`) and enforces the regex `^(main|feature/.+|bug/.+)$` on branch names. This one is not configurable from `env.json`.

## Creating the Git OAuth app for HCP Terraform

HCP Terraform needs a VCS OAuth connection before it can link a workspace to a GitHub repository. The `TF_VCS_OAUTH_TOKEN_ID` secret this workflow consumes is the **OAuth token ID** (prefix `ot-…`) produced once that connection is established. You only need to do this once per org.

### 1. Create a GitHub OAuth App

1. Go to **GitHub → Settings → Developer settings → OAuth Apps → New OAuth App** (use the org settings if the app should be org-owned).
2. Fill in:
   - **Application name** — e.g. `HCP Terraform (my-org)`
   - **Homepage URL** — `https://app.terraform.io`
   - **Authorization callback URL** — `https://app.terraform.io/auth/<uuid>/callback`. The exact URL is generated in step 2 below — create the app with any placeholder first, then come back and update this field.
3. Click **Register application**. On the next screen, copy the **Client ID** and generate a **Client secret**.

### 2. Add the VCS provider in HCP Terraform

1. In HCP Terraform, open **Settings → Version Control → Add a VCS provider → GitHub → GitHub.com (Custom)**.
2. Paste the **Client ID** and **Client secret** from step 1.
3. HCP Terraform now shows the exact **Authorization callback URL** that must be set on the GitHub OAuth App. Copy it, go back to the OAuth App settings in GitHub, and paste it into the **Authorization callback URL** field. Save.
4. Back in HCP Terraform, click **Connect and continue**. You'll be redirected to GitHub to authorise the OAuth App against your org/user.
5. After you authorise, HCP Terraform completes the setup and shows the VCS provider with an **OAuth Token ID** like `ot-aBcDeFgHiJkLmNoP`. This is the value you need.

### 3. Store the OAuth Token ID as an org secret

Add the token ID as an organisation secret in GitHub so every caller repo inherits it:

```bash
gh secret set TF_VCS_OAUTH_TOKEN_ID \
  --org <ORG> \
  --visibility all \
  --body "ot-aBcDeFgHiJkLmNoP"
```

> **Common failure:** `link-vcs` step returns HTTP 400 `invalid oauth_token_id`. Causes, in order of likelihood: (a) the secret holds the OAuth **client** ID (`oc-…`) instead of the OAuth **token** ID (`ot-…`); (b) the token belongs to a different HCP org than `hcp.hcp_org`; (c) the token was revoked. Verify with:
>
> ```bash
> curl -s -H "Authorization: Bearer $TFE_TOKEN" \
>   https://app.terraform.io/api/v2/oauth-tokens/$TF_VCS_OAUTH_TOKEN_ID | jq .
> ```

## Idempotency

| Resource                       | Mechanism                                             |
| ------------------------------ | ----------------------------------------------------- |
| HCP workspace                  | Existence check before create; step skipped if exists |
| HCP workspace variables        | `POST` — on `422` fetch var ID and `PATCH`            |
| GitHub Environments            | `PUT` is always an upsert                             |
| GitHub Environment variables   | `POST` — on `409` use `PATCH`                         |
| Repository ruleset             | List by name — `PUT` if found, `POST` if not          |

## Repository structure

```text
aws-project-onboarding/
├── .github/workflows/
│   ├── aws-project-onboarding.yaml   # The reusable workflow
│   ├── create-branch.yaml
│   └── release.yaml
├── templates/
│   ├── aws-project-onboarding.yaml   # Template copy (kept in sync)
│   └── onboard-aws-project.yaml      # Caller workflow template
├── env.json                           # Sample configuration
├── CLAUDE.md                          # AI development conventions
└── README.md
```

## Versioning

Releases follow semver. The `release.yaml` workflow moves the major-version tag (`v1`) forward automatically.

- **Patch** (`v1.0.x`) — bug fixes, no interface changes
- **Minor** (`v1.x.0`) — new optional config fields, backward compatible
- **Major** (`v2.0.0`) — breaking changes requiring migration

## License

MIT

---

**Built with [Claude Code](https://claude.ai/code)**
