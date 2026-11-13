# ci-command-truth-dogfood

A consumer repo that uses the **published** GitHub Action
`RakTikoo/ci-command-truth@v0.2` exactly as a stranger would — no vendored
copy of the tool.

The `dogfood` workflow runs two jobs, each invoking the published action
against one case directory (the action's `path` input):

- **clean** — the action scans `cases/clean`, whose own workflow asks for
  `npm run test:units`, `make check`, and `./scripts/check.sh`. All three
  exist (script in `cases/clean/package.json`, target in
  `cases/clean/Makefile`, file in `cases/clean/scripts/`).
  Expected: success — "OK — every checked command resolves."
- **phantom-detected** — the action scans `cases/phantom`, whose workflow
  asks for `npm run test:unit` while its `package.json` defines only
  `test` and `test:units` (the missing-trailing-"s" typo from the main
  repo's README demo). Expected: the action step fails with a
  `missing-npm-script` finding (plus a "did you mean 'test:units'?"
  suggestion and a `::error` annotation at `ci.yml` line 8), and the
  follow-up assertion step confirms the failure — so this job is green
  precisely because the action caught the phantom command.

This repo exists purely to prove the published Action end-to-end on real
GitHub Actions; it is not an application. The case workflows under
`cases/` are scanner input — they are never executed by GitHub.
