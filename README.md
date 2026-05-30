# straas-tech/homebrew-tap

Homebrew tap for the `straas` CLI — a squad-driven workspace tool that wires a Claude
agents+skills bundle into per-project directories and launches `claude` against them.

## Install

```sh
brew tap straas-tech/tap
brew install straas-tech/tap/straas
```

Verify:

```sh
straas --version
```

## Quickstart

1. **Activate your license** (one-time, JWT issued by the STRAAS registry):

   ```sh
   straas activate <token>
   ```

   Writes `~/.straas/license.jwt` (chmod 600).

2. **Initialize a workspace** in any project directory:

   ```sh
   cd ~/code/my-project
   straas init
   ```

   Drops `straas.toml`, copies the squad bundle into `squad/` and creates an empty
   `squad-local/` for local overrides. Idempotent — safe to re-run.

3. **Launch Claude** with the workspace squad loaded:

   ```sh
   straas claude
   ```

   Replaces the current process with `claude`, pointed at `./squad/` (or the path in
   `straas.toml [claude].plugin_dir`). Pass extra flags after `claude`:

   ```sh
   straas claude --resume
   ```

## macOS Gatekeeper / quarantine

The Sprint 2 binary is **not yet codesigned or notarized** (roadmap post-Sprint 2).
On the first run, macOS may block it with:

> "straas" cannot be opened because the developer cannot be verified.

Workaround — strip the quarantine attribute set by the browser/Homebrew download:

```sh
xattr -d com.apple.quarantine "$(brew --prefix)/bin/straas"
```

Re-run `straas --version`. If the attribute is not present, `xattr` will say
`No such xattr: com.apple.quarantine` — that is fine, you can ignore it.

## Verify the download

Each release ships a `SHA256SUMS` file next to the per-arch tarballs. To check the
tarball you downloaded matches the published checksum:

```sh
# In the directory where you downloaded the tarball + SHA256SUMS:
shasum -a 256 -c SHA256SUMS --ignore-missing
```

Expected output:

```
straas-vX.Y.Z-darwin-arm64.tar.gz: OK
```

`brew install` already verifies the formula's `sha256:` field against the downloaded
tarball, so this step is only needed if you downloaded the tarball directly from the
[Releases page](https://github.com/straas-tech/straas-workspace/releases).

## Update

```sh
brew update
brew upgrade straas
```

## Uninstall

```sh
brew uninstall straas
rm -rf ~/.straas
```

`~/.straas` holds the license JWT (`license.jwt`), the license validation cache
(`license-cache.json`), and any other CLI state. Removing it forces a fresh
`straas activate` next install.

## Troubleshooting

| Symptom | Fix |
| --- | --- |
| `straas: command not found` after install | Confirm `$(brew --prefix)/bin` is on `$PATH`. |
| `"straas" cannot be opened because the developer cannot be verified` | Run the `xattr -d com.apple.quarantine ...` command above. |
| `straas activate` rejects the token | Check token expiration; re-issue from the registry. |
| `straas claude` fails with `claude: not found` | Install Claude Code (`brew install anthropic/claude/claude`) and re-run. |

## Reporting issues

Bugs and feature requests: [straas-tech/straas-workspace issues](https://github.com/straas-tech/straas-workspace/issues).
This tap repo only hosts the formula — do not open product issues here.
