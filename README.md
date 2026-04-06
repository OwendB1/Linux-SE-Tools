# Linux Torch Wrapper

A thin wrapper that runs Torch through Wine and can fully bootstrap itself with a single command.

## One-command usage

```bash
./torch-wrapper --run
```

`--run` will:

1. Check for required runtime dependencies (`wine`, `winetricks`, `curl`, `unzip`, `cabextract`) and install them if missing.
2. On immutable rpm-ostree systems, automatically switch to a Distrobox backend and set up dependencies in a container.
3. Download the latest Torch build from:
   `https://build.torchapi.com/job/Torch/job/master/lastSuccessfulBuild/artifact/bin/torch-server.zip`
   into the script directory.
4. Ensure `./instances` exists, then let you choose an existing instance folder or create a new one.
5. For new installs, prompt for an instance folder name (default: `torch-<timestamp>` when Enter is pressed), then extract Torch there.
6. Bootstrap the Wine prefix with Winetricks dependencies in quiet mode (once per prefix).
7. Set Wine Windows version overrides to `win10` (global + `steamcmd.exe` AppDefault).
8. Apply a Wine graphics compatibility tweak (`Direct3D\\renderer=gdi`) to avoid black context/dropdown menus.
9. Launch Torch executable via host Wine or Distrobox container.

If Torch is already installed (an existing executable is detected), download is skipped unless `--install-new` is used.
When multiple Torch executables are detected, `--run` prompts you to select which instance to launch.

## Options

```bash
./torch-wrapper --run [--install-new] [--backend <auto|wine|distrobox>] [--distrobox-name <name>] [--distrobox-image <image>] [--wineprefix <path>] [--torch-dir <path>] [--instances-dir <path>] [--torch-exe <path>] [-- <torch args>]
```

- `--run`: required main action.
- `--install-new`: force installation into a newly prompted folder under `./instances` (default folder name: `torch-<timestamp>`).
- `--backend <auto|wine|distrobox>`: choose runtime backend. `auto` uses Distrobox on immutable rpm-ostree systems, otherwise Wine.
- `--distrobox-name <name>`: Distrobox container name used with `--backend distrobox` (default: `torch-wrapper`).
- `--distrobox-image <image>`: container image used for first-time Distrobox creation (default: `docker.io/library/debian:stable-slim`).
- `--wineprefix <path>`: override Wine prefix (default: `./.wine-torch` beside the wrapper).
- `--torch-dir <path>`: override Torch root used for direct executable discovery (default: `./torch` beside the wrapper).
- `--instances-dir <path>`: override instance directory (default: `./instances` beside the wrapper).
- `--torch-exe <path>`: explicitly point to an executable.
- `-- <torch args>`: pass extra arguments directly to Torch.

## Compatibility

- On immutable rpm-ostree distros (including Bazzite), the wrapper now uses Distrobox automatically.
- Distrobox mode creates a container (default name `torch-wrapper`) and programmatically enters it before continuing execution, so you do not need to manually run `distrobox-enter` first.
- After entering the container, setup/install/run steps continue there automatically, including dependency bootstrap and Torch launch.
- A host launcher shim is written to `~/.local/bin/torch-distrobox` so the containerized run path is linked back to the host.

## License

This project is licensed under the GNU General Public License v3.0 (GPL-3.0-or-later).
If you distribute modified versions, you must also provide the corresponding source code
under the same GPL terms.
