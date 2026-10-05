# Mise Config

My global mise configuration for shared, personal, and work tooling.

## Profiles

The base config provides shared settings. Each tooling stack lives in its own
directory under `conf.d/`, where mise discovers `mise.toml` for shared tools
and `mise.personal.toml` or `mise.work.toml` for profile-specific tools:

```text
conf.d/
  python/mise.toml
  go/mise.toml
  shell/mise.toml
  containers/mise.work.toml
  terminal/mise.toml
  terminal/mise.personal.toml
```

For example, the Python runtime, package manager, linter, formatter, and type
checker are grouped in `conf.d/python/mise.toml`. The Go stack has aliases and
a commented starter set of tools, but remains inactive as it was before.
`config.personal.toml` and `config.work.toml` are for profile-wide settings and
bootstrap behavior, rather than individual tool lists.

Dotfiles, shell configuration links, tmux, and TPM are shared and apply on all
machines. The personal profile additionally changes the login shell; work
bootstrap leaves the current shell setting alone.

Personal and work tools are opt-in mise environments:

```sh
mise -E personal install
mise -E work install
```

Use the same environment when bootstrapping. Both profiles apply shared
dotfiles and tmux setup.

```sh
mise -E personal bootstrap --adopt https://github.com/IanCWeston/mise-config.git
mise -E work bootstrap --adopt https://github.com/IanCWeston/mise-config.git
```

`mise -E personal ...` and `mise -E work ...` select native mise config
environments (`config.personal.toml` and `config.work.toml`). Without `-E`,
only shared stacks are active. `config.toml` requires mise 2026.10.0 or newer.
`-E` applies to that command; set `MISE_ENV=personal` or `MISE_ENV=work` in a
shell to keep a profile active there.

The work profile disables the Go stack, Node, and tools whose current locked
release URLs are bare executables rather than tar/zip archives. Taplo and
tree-sitter are also disabled because their `.gz` downloads are compressed
single binaries, not archives. Review `settings.disable_tools` in
`config.work.toml` as tool versions and release assets change. This setting is
a selection preference, not a security boundary or an uninstall mechanism.

Inspect the effective files and preview setup before applying:

```sh
mise -E personal config ls
mise -E work config ls
mise -E personal bootstrap --dry-run
mise -E work bootstrap --dry-run
```
