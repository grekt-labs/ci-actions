# grekt-actions

Reusable CI/CD setup for grekt — install the CLI and configure your environment in one step.

## GitHub Actions

### Basic usage

```yaml
jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: grekt-labs/actions/github/setup@main
        with:
          grekt-version: "latest"
          registries: |
            "@my-scope":
              url: https://cdn.grekt.com
              token: ${{ secrets.GREKT_TOKEN }}
          git-token: ${{ secrets.GITHUB_TOKEN }}

      - run: grekt install
      - run: grekt publish
```

### With Bun runtime

```yaml
      - uses: grekt-labs/actions/github/setup@main
        with:
          runtime: bun
          runtime-version: "1.1"
          registries: |
            "@my-scope":
              url: https://cdn.grekt.com
              token: ${{ secrets.GREKT_TOKEN }}
```

### Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `grekt-version` | no | `latest` | Version of grekt CLI (`latest`, `beta`, or semver) |
| `registries` | no | — | Inline YAML with registry definitions |
| `runtime` | no | `node` | `node` or `bun` |
| `runtime-version` | no | `22` | Runtime version |
| `git-user-name` | no | — | Git `user.name` for commits |
| `git-user-email` | no | — | Git `user.email` for commits |
| `git-token` | no | — | Token for authenticated git push |

### Outputs

| Output | Description |
|---|---|
| `grekt-version` | Installed grekt CLI version string |

## GitLab CI

### Basic usage

```yaml
include:
  - remote: "https://raw.githubusercontent.com/grekt-labs/actions/main/gitlab/templates/setup.yml"

release:
  extends: .grekt-setup
  variables:
    GREKT_REGISTRIES: |
      "@my-scope":
        url: https://cdn.grekt.com
        token: ${GREKT_TOKEN}
    GREKT_GIT_TOKEN: ${CI_JOB_TOKEN}
  script:
    - grekt install
    - grekt publish
```

### Variables

| Variable | Default | Description |
|---|---|---|
| `GREKT_VERSION` | `latest` | Version of grekt CLI |
| `GREKT_REGISTRIES` | — | Inline YAML with registry definitions |
| `GREKT_GIT_USER_NAME` | — | Git `user.name` for commits |
| `GREKT_GIT_USER_EMAIL` | — | Git `user.email` for commits |
| `GREKT_GIT_TOKEN` | — | Token for authenticated git push |

### Notes

The `.grekt-setup` template installs Node.js automatically if not found in the base image (supports Alpine `apk` and Debian `apt-get`). For best results, use a Node-based image like `node:22-alpine`.

## What this does

1. **Installs runtime** — sets up Node.js or Bun
2. **Installs grekt CLI** — via npm at the specified version
3. **Generates `.grekt/config.yaml`** — from your inline registry definitions
4. **Configures git** — sets user identity and authenticated remote URL

## What this does NOT do

Release flows (changelog, versioning, publish) are intentionally left to you. Every project has different release needs — this action only handles the common setup.

## License

MIT
