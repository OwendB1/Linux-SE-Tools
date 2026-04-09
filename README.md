# Linux Torch Wrapper

This repo now provides two separate launchers that share the same Wine-prefix workflow:

- `./torch-wrapper` for Torch
- `./se-toolbox-wrapper` for SEToolbox

Both scripts can install dependencies, download/extract the app, apply Wine compatibility settings (including the GDI renderer patch), and launch with Wine.

## Torch launcher

```bash
./torch-wrapper --run
```

Options:

```bash
./torch-wrapper --run [--backend <auto|wine|distrobox>] [--install-new] [--wineprefix <path>] [--torch-dir <path>] [--torch-exe <path>] [-- <torch args>]
```

Default paths:
- Wine prefix: `./.wine-torch`
- Install dir: `./torch`

## SEToolbox launcher

```bash
./se-toolbox-wrapper --run
```

Options:

```bash
./se-toolbox-wrapper --run [--backend <auto|wine|distrobox>] [--install-new] [--wineprefix <path>] [--setoolbox-dir <path>] [--setoolbox-exe <path>] [-- <setoolbox args>]
```

Default paths:
- Wine prefix: `./.wine-torch` (shared with Torch by default)
- Install dir: `./setoolbox`
- Download URL: `https://github.com/mmusu3/SEToolbox/releases/latest/download/SEToolbox.zip`

## Compatibility behavior

- Both launchers support `--backend auto|wine|distrobox`.
- On immutable systems with distrobox available, auto mode prefers distrobox.
- Both launchers apply:
  - Winetricks bootstrap (`corefonts vcrun2022 dotnet48`)
  - Windows version override to `win10`
  - Graphics fix: `HKCU\\Software\\Wine\\Direct3D\\renderer=gdi`

## License

This project is licensed under the GNU General Public License v3.0 (GPL-3.0-or-later).
If you distribute modified versions, you must also provide the corresponding source code
under the same GPL terms.
