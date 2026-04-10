# 1.0.0 (2026-04-10)


### Bug Fixes

* add missing newline at end of CODEOWNERS file ([d1f2b9e](https://github.com/subhamay-bhattacharyya-gha/aws-project-onboarding/commit/d1f2b9e364dcfcbee39138c19be9f2b42e8d6f82))
* ensure proper formatting in README.md ([fd03d33](https://github.com/subhamay-bhattacharyya-gha/aws-project-onboarding/commit/fd03d33c78df5cced8bbfd9366235be7f308051b))


### Features

* add approval environments configuration to GitHub settings in env.json ([3899eb4](https://github.com/subhamay-bhattacharyya-gha/aws-project-onboarding/commit/3899eb4f35aa3dba5ec9e0f9301b1464f2f9c78a))
* add debugging output for ruleset payload in GitHub workflow ([5e07351](https://github.com/subhamay-bhattacharyya-gha/aws-project-onboarding/commit/5e07351e7c44034fd0f934841bdbad0eb69eb301))
* add required_review_thread_resolution parameter to pull request settings ([5e51dcd](https://github.com/subhamay-bhattacharyya-gha/aws-project-onboarding/commit/5e51dcd6175fead9680b1ac225955edef10ab25e))
* document env.json structure and usage for onboarding workflow ([9f51190](https://github.com/subhamay-bhattacharyya-gha/aws-project-onboarding/commit/9f511904aba11e8879f5b354785c06970d10c2ce))
* enhance approval gate logic with reviewer count validation and detailed logging ([b22eebe](https://github.com/subhamay-bhattacharyya-gha/aws-project-onboarding/commit/b22eebe62256428e25d58b2d9a7b75b52060b836))
* enhance reviewer team access management for protected environments in GitHub workflow ([97f1d7e](https://github.com/subhamay-bhattacharyya-gha/aws-project-onboarding/commit/97f1d7efd80d0e358e372e1e948929f9a90529ad))
* implement phased reviewer attachment for protected environments in GitHub workflow ([5a648f8](https://github.com/subhamay-bhattacharyya-gha/aws-project-onboarding/commit/5a648f8df0a68287801a773734a92e0b14dc4a91))
* implement v1.0.0 aws-project-onboarding reusable workflow ([5c99d67](https://github.com/subhamay-bhattacharyya-gha/aws-project-onboarding/commit/5c99d6752766d65a0d6bb4ac2b29b44586947524))
* update approval environments to include a default test environment and add AWS OIDC role to environment variables ([d241a7c](https://github.com/subhamay-bhattacharyya-gha/aws-project-onboarding/commit/d241a7c58e5709fb094e946bb0c7fd0091f86852))
* update AWS project onboarding workflow with branch name policy and protection ruleset ([0850f64](https://github.com/subhamay-bhattacharyya-gha/aws-project-onboarding/commit/0850f64cad68734eb5db184585e647e0522ca6b3))


### BREAKING CHANGES

* first stable release of the aws-project-onboarding
platform workflow. Establishes the v1 workflow_call interface
(`config` input; `TFE_TOKEN`, `TF_VCS_OAUTH_TOKEN_ID`, `GITHUB_PAT`
secrets) that downstream caller repos must target via `@v1`.

Co-Authored-By: Claude Opus 4.6 (1M context) <noreply@anthropic.com>

## [1.1.2](https://github.com/subhamay-bhattacharyya-gha/github-action-template/compare/v1.1.1...v1.1.2) (2025-05-21)


### Bug Fixes

* update devcontainer configuration to include Node.js feature ([a6d9d47](https://github.com/subhamay-bhattacharyya-gha/github-action-template/commit/a6d9d478096bf3f7a94f5fd44e26c3deb6e2611c))

## [1.1.1](https://github.com/subhamay-bhattacharyya-gha/github-action-template/compare/v1.1.0...v1.1.1) (2025-05-19)


### Bug Fixes

* add missing permissions for issue assignment workflow ([6c1c99c](https://github.com/subhamay-bhattacharyya-gha/github-action-template/commit/6c1c99cb15f3df2cda6f7e8ea385447d43011bd7))

# [1.1.0](https://github.com/subhamay-bhattacharyya-gha/github-action-template/compare/v1.0.0...v1.1.0) (2025-05-19)


### Features

* add workflow to auto create branches on issue assignment ([c598e00](https://github.com/subhamay-bhattacharyya-gha/github-action-template/commit/c598e002938006d48354017d1131d1f43e378393))

# 1.0.0 (2025-05-18)


### Bug Fixes

* update release configuration path in package.json ([d94b611](https://github.com/subhamay-bhattacharyya-gha/github-action-template/commit/d94b61152ef216a98f5303e2ed2d78dbe309dd0e))


### Features

* add plugins for analyzing commits, generating release notes, preparing release, publishing, and verifying conditions ([60bfe70](https://github.com/subhamay-bhattacharyya-gha/github-action-template/commit/60bfe70c3415559965a970971676f25a960d884f))
* add release configuration for semantic release ([bc81687](https://github.com/subhamay-bhattacharyya-gha/github-action-template/commit/bc81687811d3ba20fb35a813afd29d222b37dbe0))
* initialize semantic release setup with custom plugins ([3a795e3](https://github.com/subhamay-bhattacharyya-gha/github-action-template/commit/3a795e3f38397cceb825de4380c5d88907a3b744))
* migrate release configuration from release.config.js to .releaserc.json ([1351e55](https://github.com/subhamay-bhattacharyya-gha/github-action-template/commit/1351e55fedf58fa9fb9bba217eecf1dba18c5a5c))
* reorder badges in README for better visibility ([f594ac6](https://github.com/subhamay-bhattacharyya-gha/github-action-template/commit/f594ac67198dddcb460c3e8f14b06ecc05dc7c36))
* update actions/checkout and actions/setup-node to v4 ([ea8ec0d](https://github.com/subhamay-bhattacharyya-gha/github-action-template/commit/ea8ec0d94afac30900f7d7229330ad4e6cc00a3d))

# Changelog

All notable changes to this project will be documented in this file.
