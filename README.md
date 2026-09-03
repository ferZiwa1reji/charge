![DragPatch](https://raw.githubusercontent.com/revolve/zfsnap/b04cbd6/assets/menu_principal.png)

# DragPatch

[![CI](https://travis-ci.org/fogproject/obs-osc.svg?branch=main)](https://travis-ci.org/fogproject/obs-osc)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Version](https://img.shields.io/badge/semver-2.0.0-green.svg)](https://semver.org/spec/v2.0.0.html)

Automates provisioning of a fresh Ubuntu/Debian machine into a complete development environment. Scripts use [`gum`](https://github.com/charmbracelet/gum) for interactive menus and support installing system utilities, shell tooling, and development stacks.

## Requirements

- Ubuntu/Debian (tested on Ubuntu 24.04)
- sudo access
- `gum` is auto-installed if not present

## Versioning

Follows [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

- Bump `MAJOR` for breaking script changes
- Bump `MINOR` for new backward-compatible features
- Bump `PATCH` for fixes and adjustments

Tag releases in Git and update the version field in this README.

## Contents

- `bootstrap.sh` — orchestrates installation, lets you select which groups to run
- `install_system_utils.sh` — Docker, Docker Compose, VS Code, Slack, Discord, Homebrew
- `install_shell_utils.sh` — Zsh, Oh My Zsh, themes/plugins, terminal tools (fzf, ripgrep, bat, zoxide)
- `install_dev_tools.sh` — Git, Make, Python, Go, Node, Postgres, sqlite, jq, httpie, GitHub CLI, Redis CLI

## Quickstart

```bash
git clone <repo>
cd DragPatch
chmod +x ./*.sh
./bootstrap.sh
```

Choose **Install all** or select individual groups.

- **Install all** runs all three modules in sequence and exports `DRAGPATCH_INSTALL_ALL=1` so each installer applies all options automatically.
- All menu options are pre-selected by default — press `Enter` to accept, `Space` to deselect.
- To skip menus entirely (e.g. in CI): `DRAGPATCH_INSTALL_ALL=1 ./bootstrap.sh`

## Testing in a VM (Multipass)

1. **Create/reset VM**
   ```bash
   multipass stop dragpatch-test 2>/dev/null || true
   multipass delete dragpatch-test 2>/dev/null || true
   multipass purge || true
   multipass launch 24.04 --name dragpatch-test
   ```
2. **Copy repo into VM**
   ```bash
   multipass transfer -r ~/workspace/DragPatch dragpatch-test:/home/ubuntu/
   ```
3. **Run bootstrap**
   ```bash
   multipass exec dragpatch-test -- bash -lc 'cd ~/DragPatch && TERM=xterm ./bootstrap.sh'
   ```
4. **Quick validation**
   ```bash
   multipass exec dragpatch-test -- docker --version
   multipass exec dragpatch-test -- zsh --version
   multipass exec dragpatch-test -- git --version
   ```

## Design Notes

- **Safe menus:** `mapfile` captures `gum` selections preserving entries with spaces
- **Idempotency:** Oh My Zsh, Spaceship theme, and plugins check for existing installs before cloning
- **Fallbacks:** if `exa` is unavailable on Ubuntu 24.04, the script falls back to `eza`
- **Non-interactive Zsh:** `sudo chsh -s "$(which zsh)" "$USER"` avoids TTY blocks in unattended runs

## License

Distributed under the MIT License. See [`LICENSE`](LICENSE).