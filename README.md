# Linux Torch Wrapper

A thin wrapper that runs Torch and SEToolbox through Wine and can bootstrap either app with a single command.

## One-command usage

```bash
./torch-se-wrapper --run
```

`--run` will:

1. Resolve runtime backend based on `--backend`:
   - `auto` (default): interactive prompt on mutable systems to choose `wine` vs `distrobox`; defaults to `distrobox` on immutable systems.
   - `wine`: run directly on host.
   - `distrobox`: create/enter distrobox, then reinvoke wrapper with `--backend wine` inside the container.
2. Resolve app target based on `--app`:
   - `auto` (default): detect existing binaries and select `torch` or `setoolbox`.
   - `torch`: run Torch.
   - `setoolbox`: run Space Engineers Toolbox.
3. Check for required Linux dependencies (`wine`, `winetricks`, `curl`, `unzip`) and install them if missing.
4. Download and extract the selected app when no executable is found:
   - Torch from `https://build.torchapi.com/job/Torch/job/master/lastSuccessfulBuild/artifact/bin/torch-server.zip`
   - SEToolbox from `https://github.com/mmusu3/SEToolbox/releases/latest/download/SEToolbox.zip`
5. Bootstrap Wine prefix dependencies (once per prefix).
6. Set Wine Windows version overrides to `win10` (global + `steamcmd.exe` AppDefault).
7. Apply the Wine graphics compatibility patch (`Direct3D\\renderer=gdi`) for both Torch and SEToolbox.
8. Launch the selected executable via Wine.

If binaries are already installed (an existing executable is detected), download is skipped unless `--install-new` is used.
When multiple binaries are detected, `--run` prompts you to select which one to launch.

## Options

```bash
./torch-se-wrapper --run [--app <auto|torch|setoolbox>] [--backend <auto|wine|distrobox>] [--install-new] [--wineprefix <path>] [--torch-dir <path>] [--setoolbox-dir <path>] [--torch-exe <path>] [--setoolbox-exe <path>] [-- <app args>]
```

- `--run`: required main action.
- `--app <auto|torch|setoolbox>`: choose app target (default: `auto`).
- `--backend <auto|wine|distrobox>`: choose execution backend (default: `auto`).
- `--install-new`: force installation of a new instance for the selected app.
- `--wineprefix <path>`: override Wine prefix (default: `./.wine-torch` beside the wrapper).
- `--torch-dir <path>`: override Torch install directory (default: `./torch`).
- `--setoolbox-dir <path>`: override SEToolbox install directory (default: `./setoolbox`).
- `--torch-exe <path>`: explicitly point to a Torch executable.
- `--setoolbox-exe <path>`: explicitly point to a SEToolbox executable.
- `-- <app args>`: pass extra arguments directly to the selected app.

## Compatibility

- `./torch-wrapper` is kept as a compatibility shim and forwards to `./torch-se-wrapper`.

## License

This project is licensed under the GNU General Public License v3.0 (GPL-3.0-or-later).
If you distribute modified versions, you must also provide the corresponding source code
under the same GPL terms.
