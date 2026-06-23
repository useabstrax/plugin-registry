# Abstrax Plugin Registry

Source definitions for the public [Abstrax plugin registry](https://plugins.useabstrax.com). Users install plugins from the registry with `abstrax plugin install`.

**To list a plugin in the registry, open a pull request in this repository.** Each plugin is a single YAML file under [`plugins/`](plugins/). After review and merge, Abstrax imports the definition into the live registry.

## Before you submit

Build and test your plugin first. You will need:

- A plugin binary for each supported platform (`linux-amd64`, `linux-arm64`)
- A working `plugin metadata` command that prints valid JSON to stdout
- Semantic versioned releases hosted at stable HTTPS URLs
- SHA-256 checksums for every published binary
- A public source repository, licence, and documentation URL

The end-to-end author workflow is documented on the Abstrax site:

- [Creating a plugin](https://useabstrax.com/docs/plugins/creating-a-plugin)
- [Metadata protocol](https://useabstrax.com/docs/plugins/metadata-protocol)
- [Integrating with Abstrax](https://useabstrax.com/docs/plugins/integrating-with-abstrax)
- [Plugin security](https://useabstrax.com/docs/plugins/security)

The reference implementation lives in the [abstrax](https://github.com/useabstrax/abstrax) repository under `plugin-example/`.

## How to submit

1. Fork [useabstrax/plugin-registry](https://github.com/useabstrax/plugin-registry).
2. Copy one of the files from [`examples/`](examples/) as a starting point.
3. Create `plugins/<slug>.yml` with your plugin metadata and version records.
4. Verify your plugin locally:
   ```bash
   abstrax-<name> plugin metadata
   ```
5. Open a pull request against `main`.

Put the YAML file in **`plugins/`**, not `examples/`. The `examples/` directory is reference material only.

### Pull request checklist

Include everything reviewers need to verify your submission:

| Item | Notes |
|---|---|
| Plugin name and display name | Must match your `abstrax-<name>` binary suffix |
| Short and long description | Plain text; HTML is stripped on import |
| Publisher name and slug | Your organisation or GitHub username |
| Public source repository | HTTPS URL to the plugin source |
| Licence and documentation URL | SPDX-style licence string and docs link |
| Release manifest URL | Per-version manifest served over HTTPS |
| Supported platforms | `linux-amd64` and/or `linux-arm64` |
| Semantic version | Valid semver for each release |
| `requires_abstrax` | Semver constraint, for example `>=0.1.0` |
| SHA-256 checksums | Lowercase hex, 64 characters, for each binary |
| Security contact | Email or URL where security issues can be reported |
| `plugin metadata` output | Confirm it works and matches the YAML |

Release manifest format and checksum requirements: [Registry API - release manifest format](https://useabstrax.com/docs/plugins/registry-api#release-manifest-format).

## YAML format

Each file defines one plugin:

```yaml
plugin:
  name: myplugin
  slug: myplugin
  display_name: My Plugin
  description: Short one-line summary
  long_description: |
    Longer description shown on the registry listing page.
  trust_level: community
  status: active
  homepage_url: https://plugins.useabstrax.com/plugins/myplugin
  repository_url: https://github.com/you/abstrax-myplugin
  documentation_url: https://example.com/docs
  license: MIT

publisher:
  name: Your Name
  slug: your-slug

versions:
  - version: 1.0.0
    channel: stable
    protocol_version: 1
    requires_abstrax: ">=0.1.0"
    release_date: "2026-06-15T12:00:00Z"
    manifest_url: https://example.com/myplugin/1.0.0/manifest.json
    status: active
    binaries:
      linux-amd64:
        url: https://example.com/releases/myplugin/1.0.0/linux-amd64/abstrax-myplugin
        sha256: "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa"
        size: 123456
      linux-arm64:
        url: https://example.com/releases/myplugin/1.0.0/linux-arm64/abstrax-myplugin
        sha256: "bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb"
        size: 123456
```

### Rules

- **`plugin.name` and `plugin.slug` must match.** Use lowercase letters, digits, and hyphens only. The name must start with a letter and be at most 63 characters.
- **File name:** `plugins/<slug>.yml` (or `.yaml`).
- **Binary names:** plugins are invoked as `abstrax-<name>`. See [plugin naming](https://useabstrax.com/docs/plugins/how-it-works#plugin-naming-convention).
- **Platforms:** only `linux-amd64` and `linux-arm64` are supported.
- **URLs:** production release and manifest URLs must use HTTPS.
- **Trust level:** third-party submissions should use `trust_level: community`. The `official` level is reserved for plugins published by Abstrax.
- **Reserved names:** the following plugin names cannot be used because they conflict with built-in Abstrax commands: `version`, `self`, `doctor`, `config`, `user`, `ssh-key`, `ssh`, `package`, `service`, `cron`, `daemon`, `project`, `web`, `ssl`, `mysql`, `cache`, `firewall`, `server`, `log`, `agent`, `plugin`.

Field definitions and enums are in [`schema.json`](schema.json). Working examples are in [`examples/example.yml`](examples/example.yml) and [`examples/deploy.yml`](examples/deploy.yml).

## Review

Submissions are reviewed manually. Acceptance is not guaranteed.

Registry inclusion is **not** a full security audit. Trust levels (`official`, `verified`, `community`) and status values (`active`, `deprecated`, `blocked`) are policy metadata, not proof that a plugin is safe. Read [Plugin security](https://useabstrax.com/docs/plugins/security) and [Registry](https://useabstrax.com/docs/plugins/registry) before publishing.

## After approval

When your pull request is merged, Abstrax imports the YAML into the live registry. The plugin then appears at `https://plugins.useabstrax.com/plugins/<slug>` and can be installed with:

```bash
sudo abstrax plugin install <name>
```

To publish a new version later, open another pull request that adds or updates the relevant entry under `versions:` in your plugin file.

## Documentation

| Topic | Link |
|---|---|
| Plugins overview | [useabstrax.com/docs/plugins](https://useabstrax.com/docs/plugins) |
| Registry submission (full requirements) | [Registry submission](https://useabstrax.com/docs/plugins/registry-submission) |
| Registry API and manifest format | [Registry API](https://useabstrax.com/docs/plugins/registry-api) |
| Plugin commands (`install`, `search`, etc.) | [Plugin commands](https://useabstrax.com/docs/commands/plugins) |
| Browse the registry | [plugins.useabstrax.com](https://plugins.useabstrax.com) |

## Repository layout

```
plugin-registry/
├── plugins/          # Submit your YAML here (one file per plugin)
├── examples/         # Reference YAML - do not submit here
├── schema.json       # JSON Schema for plugin definitions
└── README.md
```
