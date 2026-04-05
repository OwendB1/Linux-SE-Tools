# Linux Torch Wrapper

A thin wrapper that runs Torch through Wine and can fully bootstrap itself with a single command.

## One-command usage

```bash
./torch-wrapper --run
```

`--run` will:

1. Check for required Linux dependencies (`wine`, `winetricks`, `curl`, `unzip`, `cabextract`) and install them if missing (Wine backend).
2. Download the latest Torch build from:
   `https://build.torchapi.com/job/Torch/job/master/lastSuccessfulBuild/artifact/bin/torch-server.zip`
   into the script directory.
3. Ensure `./instances` exists, then let you choose an existing instance folder or create a new one.
4. For new installs, prompt for an instance folder name (default: `torch-<timestamp>` when Enter is pressed), then extract Torch there.
5. Bootstrap the Wine prefix with Winetricks dependencies (once per prefix).
6. Set Wine Windows version overrides to `win10` (global + `steamcmd.exe` AppDefault).
7. Apply a Wine graphics compatibility tweak (`Direct3D\\renderer=gdi`) to avoid black context/dropdown menus.
8. Launch Torch executable via Wine or Bottles.

If Torch is already installed (an existing executable is detected), download is skipped unless `--install-new` is used.
When multiple Torch executables are detected, `--run` prompts you to select which instance to launch.

## Options

```bash
./torch-wrapper --run [--install-new] [--backend <auto|wine|bottles>] [--bottle <name>] [--wineprefix <path>] [--torch-dir <path>] [--instances-dir <path>] [--torch-exe <path>] [-- <torch args>]
```

- `--run`: required main action.
- `--install-new`: force installation into a newly prompted folder under `./instances` (default folder name: `torch-<timestamp>`).
- `--backend <auto|wine|bottles>`: choose runtime backend. `auto` uses Bottles on Bazzite (when available), otherwise Wine.
- `--bottle <name>`: Bottles bottle name to use with `--backend bottles` (default: `Torch`).
- `--wineprefix <path>`: override Wine prefix (default: `./.wine-torch` beside the wrapper).
- `--torch-dir <path>`: override Torch root used for direct executable discovery (default: `./torch` beside the wrapper).
- `--instances-dir <path>`: override instance directory (default: `./instances` beside the wrapper).
- `--torch-exe <path>`: explicitly point to an executable.
- `-- <torch args>`: pass extra arguments directly to Torch.

## Compatibility

- `./torch-wrapper.sh` is kept as a compatibility shim and forwards to `./torch-wrapper`.
- On immutable rpm-ostree distros (including Bazzite), automatic package installation is intentionally blocked. Install dependencies via:
  `rpm-ostree install wine winetricks curl unzip cabextract`, reboot, then run `./torch-wrapper --run`.
- On Bazzite, Bottles is also supported. Install Bottles (for example via Flatpak) and run:
  `./torch-wrapper --run --backend bottles` (or leave `--backend auto`).
- When using Bottles backend, the wrapper attempts the same bootstrap/tweaks flow in that bottle (`corefonts vcrun2022 dotnet48`, win10 override, and `Direct3D\\renderer=gdi`).

## License

This project is licensed under the GNU General Public License v3.0 (GPL-3.0-or-later).
If you distribute modified versions, you must also provide the corresponding source code
under the same GPL terms.
