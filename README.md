# Geol Action

![GitHub License](https://img.shields.io/github/license/opt-nc/geol-action)

GitHub Action to install [geol](https://github.com/opt-nc/geol) in your GitHub Actions workflows.

[![Geol Action demo video thumbnail](https://github.com/user-attachments/assets/019641ba-4f9d-4f89-b296-3694bf472f85)](https://www.youtube.com/watch?v=0havqKL-Suo)

## 📖 About

`geol` is a Go-based CLI tool to efficiently manage and monitor End Of Life (EOL) dates for various products and technologies, directly from your terminal and CI/CD pipelines, using the [endoflife.date](https://endoflife.date/) API.

This GitHub Action makes it easy to integrate `geol` into your workflows to automate EOL version checks for your dependencies and tech stack.

## 🚀 Usage

### Basic example

```yaml
name: Check EOL

on: [push, pull_request]

jobs:
  check-eol:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@de0fac2e4500dabe0009e67214ff5f5447ce83dd

      - name: Install geol
        uses: opt-nc/geol-action
        with:
          version: 'v2.12.3' # Optional: specify a version or use 'latest'

      - name: Check stack EOL
        run: |
          geol check
```

### Automated check + issue reporting

Instead of scripting `geol check` and issue creation yourself, you can let the action do it from plain YAML inputs. Set `strict: true` and, if `geol check` fails (something is EOL or not on its latest version), the action automatically creates/updates a GitHub issue with a clean report **and** fails the step:

```yaml
name: Check EOL

on:
  schedule:
    - cron: '0 6 * * 1' # every Monday
  workflow_dispatch:

permissions:
  issues: write

jobs:
  check-eol:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@de0fac2e4500dabe0009e67214ff5f5447ce83dd

      - name: Check EOL and open/update an issue
        uses: opt-nc/geol-action
        with:
          strict: 'true'                # implies run-check: true; fails the step + opens an issue if EOL
          file: '.geol.yaml'            # geol stack file, default: .geol.yaml
          issue-title: '🚨 EOL report - {{app_name}}'
          eol-label: 'eol'              # applied when a product is past EOL
          warning-label: 'eol:warning'  # applied when a product nears EOL
          warning-threshold-days: '90'  # "nearing EOL" window
          labels: 'tech-debt,security'  # extra labels always applied
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

This generates (or updates in place) a single open issue per stack, with a clean Markdown table — no raw CLI logs — only when `geol check --strict` fails, and closes it automatically once the stack is healthy again.

| Input                     | Description                                                                 | Default                        |
| ------------------------- | ---------------------------------------------------------------------------- | ------------------------------- |
| `version`                  | `geol` version to install                                                    | `latest`                        |
| `run-check`                | Run `geol check` right after install (auto-enabled by `strict`)              | `false`                          |
| `file`                     | Path to the geol stack YAML file                                            | `.geol.yaml`                    |
| `date`                     | Reference date for EOL calculations (`YYYY-MM-DD`)                          | today                            |
| `strict`                   | Fail the step if any product is EOL/outdated, and create/update the report issue | `false`                     |
| `issue-title`              | Issue title template, supports `{{app_name}}`, `{{date}}`, `{{score}}`       | `EOL report - {{app_name}}`     |
| `eol-label`                | Label applied when at least one product is past EOL                         | `eol`                            |
| `warning-label`            | Label applied when no product is EOL but some are nearing EOL               | `eol:warning`                    |
| `warning-threshold-days`   | Days before EOL under which a product is considered "nearing EOL"           | `90`                             |
| `labels`                   | Comma-separated extra labels always applied to the issue                    | `''`                             |
| `github-token`             | Token used to create/update/close the issue, required when `strict: true`   | `''`                             |

Outputs `eol-count`, `warning-count` and `score` are also available for use in later steps.


## 🔧 How it works

This action:

1. Downloads the specified version of `geol` (or the latest if not specified) from the GitHub releases
2. Makes `geol` available for the next workflow steps
3. Optionally runs `geol check --json`, builds a clean Markdown report, and creates/updates/closes a GitHub issue accordingly, using `gh` and `jq` (both preinstalled on GitHub-hosted runners)

## 📚 geol Documentation

For more information on using `geol`, see:

- [Official geol documentation](https://opt-nc.github.io/geol/)
- [geol GitHub repository](https://github.com/opt-nc/geol)
- [endoflife.date API](https://endoflife.date/docs/api/v1/)

## 🤝 Contributing

Contributions are welcome! Feel free to open an issue or pull request.

## 📄 License

This GitHub Action is distributed under the Apache-2.0 license. See the [LICENSE](LICENSE) file for details.
