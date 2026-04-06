# Linux Torch Wrapper

A thin wrapper that runs Torch through Wine and can fully bootstrap itself with a single command.

## One-command usage

```bash
./torch-wrapper --run
```

`--run` will:

1. Resolve runtime backend based on `--backend`:
   - `auto` (default): interactive prompt on mutable systems to choose `wine` vs `distrobox`; defaults to `distrobox` on immutable systems.
   - `wine`: run directly on host.
   - `distrobox`: create/enter distrobox, then reinvoke wrapper with `--backend wine`.
2. Check for required Linux dependencies (`wine`, `winetricks`, `curl`, `unzip`) and install them if missing.
3. Download the latest Torch build from:
   `https://build.torchapi.com/job/Torch/job/master/lastSuccessfulBuild/artifact/bin/torch-server.zip`
   into the script directory.
4. Extract Torch to `./torch` (relative to the script location).
5. Bootstrap the Wine prefix with Winetricks dependencies (once per prefix).
6. Set Wine Windows version overrides to `win10` (global + `steamcmd.exe` AppDefault).
7. Apply a Wine graphics compatibility tweak (`Direct3D\\renderer=gdi`) to avoid black context/dropdown menus.
8. Launch Torch executable via Wine.

If Torch is already installed (an existing executable is detected), download is skipped unless `--install-new` is used.
When multiple Torch executables are detected, `--run` prompts you to select which instance to launch.

## Options

```bash
./torch-wrapper --run [--backend <auto|wine|distrobox>] [--install-new] [--wineprefix <path>] [--torch-dir <path>] [--torch-exe <path>] [-- <torch args>]
```

- `--run`: required main action.
- `--backend <auto|wine|distrobox>`: choose execution backend (default: `auto`).
- `--install-new`: force installation of a new Torch instance into `./torch/instances/torch-<timestamp>`.
- `--wineprefix <path>`: override Wine prefix (default: `./.wine-torch` beside the wrapper).
- `--torch-dir <path>`: override install directory (default: `./torch` beside the wrapper).
- `--torch-exe <path>`: explicitly point to an executable.
- `-- <torch args>`: pass extra arguments directly to Torch.

## Compatibility

- `./torch-wrapper.sh` is kept as a compatibility shim and forwards to `./torch-wrapper`.

## License

This project is licensed under the GNU General Public License v3.0 (GPL-3.0-or-later).
If you distribute modified versions, you must also provide the corresponding source code
under the same GPL terms.
