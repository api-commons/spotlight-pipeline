<p align="center"><a href="https://spotlight-rules.com"><img src="https://raw.githubusercontent.com/api-commons/spotlight-pipeline/main/spotlight-rules-logo.png" alt="Spotlight Rules" height="90"></a></p>

# Spotlight Pipeline

**Governance as code.** Drop your [Spotlight](https://spotlight-rules.com) ruleset into
CI and every change to your API descriptions is linted against it — as a gate. This
repo is a set of **thin, reusable CI integrations** that all run
[spotlight-cli](https://github.com/api-commons/spotlight-cli) the same way, with the
same inputs (files, ruleset, severity threshold) across platforms.

The unit you "deploy" is **your ruleset + a fail-severity** — see
[`.spotlight.example.yaml`](./.spotlight.example.yaml).

## GitHub Actions

Use the action directly:

```yaml
# .github/workflows/api-governance.yml
on: [pull_request]
jobs:
  spotlight:
    runs-on: ubuntu-latest
    permissions: { contents: read, security-events: write }
    steps:
      - uses: actions/checkout@v4
      - uses: api-commons/spotlight-pipeline@v1
        with:
          files: 'apis/**/openapi.yaml'
          ruleset: '.spotlight.yaml'
          fail-severity: 'error'
          sarif: 'spotlight.sarif'           # optional → code scanning
      - if: always()
        uses: github/codeql-action/upload-sarif@v3
        with: { sarif_file: spotlight.sarif }
```

| Input | Default | Description |
| --- | --- | --- |
| `files` | `**/{openapi,swagger,asyncapi,arazzo}.{yaml,yml,json}` | Glob(s) to lint |
| `ruleset` | `.spotlight.yaml` | Your ruleset (a default extending the built-ins is used if absent) |
| `fail-severity` | `error` | Fail on this severity or worse (`error`/`warn`/`info`/`hint`) |
| `sarif` | _(off)_ | Also write a SARIF report for GitHub code scanning |
| `version` | `latest` | spotlight-cli version |

It emits inline **PR annotations** (`--format github-actions`) and a readable summary,
and fails the job per `fail-severity`.

## Other platforms

Copy the matching template from [`templates/`](./templates) — all run the same
`spotlight-cli lint … --ruleset … --fail-severity …`, publishing findings natively:

- **GitLab CI** — [`templates/gitlab-ci.yml`](./templates/gitlab-ci.yml) (JUnit report)
- **Bitbucket Pipelines** — [`templates/bitbucket-pipelines.yml`](./templates/bitbucket-pipelines.yml)
- **Azure DevOps** — [`templates/azure-pipelines.yml`](./templates/azure-pipelines.yml) (JUnit)
- **AWS CodeBuild** — [`templates/aws-buildspec.yml`](./templates/aws-buildspec.yml) (JUnit report)

## Part of the Spotlight suite

One engine (the CLI), one vocabulary (the [spec](https://spotlight-rules.com/spec/) +
its rule tags): spec · cli · api · mcp · vscode · validator · discovery · **pipeline**
(this repo — governance in delivery). Author rules in the
[validator](https://validator.spotlight-rules.com), enforce them here.

---

Part of [Spotlight Rules](https://spotlight-rules.com) — a project of [API Evangelist](https://apievangelist.com), maintained openly under [API Commons](https://apicommons.org). Apache-2.0.
