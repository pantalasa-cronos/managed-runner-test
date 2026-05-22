# managed-runner-test

Test repo verifying the Lunar CI Agent works on a GitHub-managed runner
(`runs-on: ubuntu-latest`) via the `earthly/lunar-ci-action` install
path, instead of the usual self-hosted `runs-on: cronos` pattern.

## Why

The cronos demo env has historically used a self-hosted runner with the
lunar CI agent wired in via systemd. This repo exercises the other
installation path documented at
[docs-lunar.earthly.dev](https://docs-lunar.earthly.dev) — installing
the agent as a workflow step on a GitHub-hosted runner. It's a
prerequisite for the 1000-repo cronos load test (ephemeral / on-demand
runners), tracked in
[cronos-load-test-implementation.md](https://github.com/brandonSc/earthly-agent-config/blob/main/plans/cronos-load-test-implementation.md).

## What the workflow does

`.github/workflows/ci.yml`:

1. Runs on `ubuntu-latest` (GitHub-managed runner).
2. First step: `earthly/lunar-ci-action@v1.1.4` — downloads and attaches
   the agent via ptrace. `LUNAR_HUB_HOST` is `cronos.demo.earthly.dev`;
   `LUNAR_HUB_TOKEN` is pulled from the repo's Actions secret of the
   same name.
3. Subsequent steps run trivial build/test commands (go build,
   go test) so the agent has something to trace.

Once the workflow runs, cronos picks up the component (registered in
`pantalasa-cronos/lunar/lunar-config.yml`), the `github-actions`
collector processes the CI run, and the resulting data lands in the
Component JSON on the cronos hub.

## Required repo secret

| Secret | Value |
|---|---|
| `LUNAR_HUB_TOKEN` | Hub auth token for `cronos.demo.earthly.dev` |

Updated: 2026-04-24T13:05:33Z — retest after manifest merge

<!-- cronos-trigger: 2026-05-22T02:22:42Z -->