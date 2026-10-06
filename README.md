# Mise Config

Global mise configuration for shared, personal, and work environments.

## Design

`config.toml` contains shared settings and machine setup. Tool stacks are
organized by domain under `conf.d/`: each `mise.toml` fragment is shared, while
`mise.personal.toml` and `mise.work.toml` fragments are loaded only for their
matching environment. The profile config files hold environment-wide settings
and bootstrap behavior.

## Usage

Select a profile for an individual command with `-E`, or set `MISE_ENV` for a
shell session:

```sh
mise -E personal install
mise -E work bootstrap
MISE_ENV=work mise install
```

## Offline use

Prepare a machine while it has network access, using the same profile, mise
version, platform, and architecture you plan to use offline. Install the
configured tools in advance; `mise.lock` records resolved versions and download
metadata but does not contain the tool binaries.

```sh
mise -E work install
MISE_OFFLINE=1 mise -E work install
```

The offline command can use tools already installed locally, but cannot fetch
missing tools or bootstrap network resources such as Git repositories. For a
disconnected machine, provide the prepared mise install data and any
backend-specific caches it needs. `--locked` checks for lockfile entries; it
does not make an installation offline.
