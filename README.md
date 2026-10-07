# Mise Config

Global mise configuration for shared, personal, and work environments.

## Design

`config.toml` contains shared settings and machine setup. Tool stacks are
organized by domain under `conf.d/`: each `mise.toml` fragment is shared, while
`mise.personal.toml` and `mise.work.toml` fragments are loaded only for their
matching environment. The profile config files hold environment-wide settings
and bootstrap behavior. Node.js and npm-backed tools are personal-only and
remain split across their owning stacks, so the work profile's Node exclusion
does not select Node-dependent tools without the runtime.

## Supported platforms

The bootstrap package declarations target Ubuntu (`apt`) and Fedora (`dnf`).
WSL uses the Ubuntu package path; its personal profile uses a WSL-only `sudo
chsh` workaround for PAM, followed by mise's native login-shell management.
GitHub Actions validates both Ubuntu and Fedora config loading and bootstrap
plans and simulates the WSL fallback; WSL itself is not a hosted CI runner.

## Usage

The global `miserc.toml` defaults to the `personal` profile. Select a profile
for an individual command with `-E`, set `MISE_ENV` for a shell session, or use
the untracked `miserc.local.toml` in `~/.config/mise` to select a machine-wide
profile without changing the shared configuration:

```sh
mise -E personal install
mise -E work bootstrap
MISE_ENV=work mise install
```

For a machine that should default to the work profile, create
`~/.config/mise/miserc.local.toml` with:

```toml
env = ["work"]
```

The local file is git-ignored. `MISE_ENV` and `mise -E` explicitly override
the config-file selection; project `.miserc.toml` files can also select an
environment for a project. If a shell already has `MISE_ENV` exported, unset
it or start a fresh login session for the local selection to take effect.

## Reconcile a machine

Re-run bootstrap to apply the selected profile's declared packages, repositories,
dotfiles, shell setup, and tools. For a personal Ubuntu/WSL machine, run this
from an interactive terminal so the WSL `sudo chsh` hook can authenticate:

```sh
mise -E personal bootstrap --yes
```

To refresh configured repositories and package metadata and overwrite conflicting
managed dotfiles, use:

```sh
mise -E personal bootstrap --yes --update --force-dotfiles
```

Review and commit or stash local changes in managed repositories before using
`--update`; bootstrap stops rather than updating a dirty repository. Avoid
`--skip-dirty` when reconciling to the configured state, since it skips updating
those repositories. `--force-dotfiles` only replaces conflicting managed
dotfiles; bootstrap does not remove unmanaged files or packages.

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
