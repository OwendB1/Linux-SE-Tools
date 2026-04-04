# Linux Torch Wrapper

A thin wrapper that runs Torch through Wine and can fully bootstrap itself with a single command.

## One-command usage

```bash
./torch-wrapper --run
```

`--run` will:

1. Check for required Linux dependencies (`wine`, `winetricks`, `curl`, `unzip`) and install them if missing.
2. Download the latest Torch build from:
   `https://build.torchapi.com/job/Torch/job/master/lastSuccessfulBuild/artifact/bin/torch-server.zip`
   into the script directory.
3. Extract Torch to `./torch` (relative to the script location).
4. Bootstrap the Wine prefix with Winetricks dependencies (once per prefix).
5. Set Wine Windows version overrides to `win10` (global + `steamcmd.exe` AppDefault).
6. Launch Torch executable via Wine.

If Torch is already installed (an existing executable is detected), download is skipped.

## Options

```bash
./torch-wrapper --run [--wineprefix <path>] [--torch-dir <path>] [--torch-exe <path>] [-- <torch args>]
```

- `--run`: required main action.
- `--wineprefix <path>`: override Wine prefix (default: `./.wine-torch` beside the wrapper).
- `--torch-dir <path>`: override install directory (default: `./torch` beside the wrapper).
- `--torch-exe <path>`: explicitly point to an executable.
- `-- <torch args>`: pass extra arguments directly to Torch.

## Compatibility

- `./torch-wrapper.sh` is kept as a compatibility shim and forwards to `./torch-wrapper`.
