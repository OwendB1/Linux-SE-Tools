# Linux-SE-Tools

Linux-SE-Tools provides separate launchers for Space Engineers tooling on Linux:

- `./torch-wrapper` for Torch
- `./se-toolbox-wrapper` for SEToolbox
- `./nexus-controller-wrapper` for Nexus Controller

All launchers can install dependencies, download/extract the app, apply Wine compatibility settings, and launch with Wine. Torch and SEToolbox share the same Wine-prefix workflow by default. Nexus Controller uses its own prefix by default because it has a different dependency profile and also bootstraps native PostgreSQL on the host unless you opt out with `--skip-postgres`.

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
./se-toolbox-wrapper --run [--backend <auto|wine|distrobox>] [--install-new] [--wineprefix <path>] [--setoolbox-dir <path>] [--setoolbox-exe <path>] [--steam-install-dir <path>] [-- <setoolbox args>]
```

Default paths:
- Wine prefix: `./.wine-torch` (shared with Torch by default)
- Install dir: `./setoolbox`
- Download asset selection: scans latest release assets for `SEToolbox-*.zip` via GitHub API (falls back to `https://github.com/mmusu3/SEToolbox/releases/latest/download/SEToolbox.zip`)
- Steam app library linking: auto-detects host Steam install and links only `steamapps/common` into `C:\Program Files (x86)\Steam\steamapps\common` inside the Wine prefix (override host root with `--steam-install-dir <path>`). If host Steam or `steamapps/common` is missing, the wrapper just creates/uses a local `C:\Program Files (x86)\Steam\steamapps` directory and does not link or install full Steam.
- Space Engineers roaming data linking: links `steamapps/compatdata/244850/pfx/drive_c/users/steamuser/AppData/Roaming/SpaceEngineers` into each non-system Wine profile at `{user}\AppData\Roaming\SpaceEngineers`
- Steam App ID environment: exports `SteamAppId` and `SteamGameId` before launch (defaults to `244850`; override with `STEAM_APP_ID=<id>`)

## Nexus Controller launcher

```bash
./nexus-controller-wrapper --run --license-guid <GUID>
```

Options:

```bash
./nexus-controller-wrapper --run [--backend <auto|wine|distrobox>] [--install-new] [--license-guid <guid>] [--wineprefix <path>] [--nexus-dir <path>] [--nexus-exe <path>] [--sql-database <name>] [--sql-user <name>] [--sql-password <password>] [--skip-postgres] [-- <controller args>]
```

Default paths and behavior:
- Wine prefix: `./.wine-nexus-controller`
- Install dir: `./nexus-controller/bin`
- Download source: `https://api.se-nexus.net/download?license=<GUID>`
- License GUID source: `--license-guid <GUID>` or `NEXUS_LICENSE_GUID=<GUID>`
- Winetricks bootstrap: `corefonts vcrun2022` by default (override with `NEXUS_WINETRICKS_VERBS`)
- .NET runtime bootstrap: installs Windows `.NET 8 Desktop Runtime` in Wine from `https://aka.ms/dotnet/8.0/windowsdesktop-runtime-win-x64.exe`, and now enforces minimum version `8.0.18` (override URL with `NEXUS_DOTNET8_DESKTOP_RUNTIME_URL`, minimum with `NEXUS_DOTNET8_MIN_VERSION`)
- Native PostgreSQL: installed and started on the host by default; this step needs root/sudo access. Wrapper tries systemd first and falls back to `pg_ctl` when possible. If current shell cannot elevate, wrapper prints manual commands. Use `--skip-postgres` if you manage PostgreSQL yourself
- Nexus SQL defaults: provisions PostgreSQL database `ControllerData` and role `postgres` on port `5432`. Fresh install bootstrap uses `defaultpass` until controller creates `Config/NexusControlConfig.cfg`. After config exists, wrapper preserves existing non-default password or generates random per-prefix password once, rewrites `SQLDatabase`, `SQLUsername`, and `SQLPassword`, and reuses that password on later runs. Override with `--sql-database`, `--sql-user`, `--sql-password` or `NEXUS_SQL_DATABASE`, `NEXUS_SQL_USERNAME`, `NEXUS_SQL_PASSWORD`

Nexus-specific notes from official docs:
- Extract controller into a lower `bin` directory under a parent folder because sibling folders are created next to `bin`
- First activation binds machine and IP to the license, so only activate on target host
- Nexus Controller is officially supported on Windows 10+/Windows Server 2016+; Linux/Wine usage is best-effort
- PostgreSQL is required for NexusV3 controller operation
- Controller/plugin ecosystem targets `.NET 8` / `net8.0-windows`
- If you use `--backend distrobox`, configure SQL hostname to a host-reachable address instead of `localhost` when PostgreSQL stays on the host

## Compatibility behavior

- All launchers support `--backend auto|wine|distrobox`.
- On immutable systems with distrobox available, auto mode prefers distrobox.
- All launchers apply:
  - Windows version override to `win10`
  - Graphics fix: `HKCU\\Software\\Wine\\Direct3D\\renderer=gdi`
- Torch and SEToolbox also apply:
  - Winetricks bootstrap (`corefonts vcrun2022 dotnet48`)

## License

This project is licensed under the GNU General Public License v3.0 (GPL-3.0-or-later).
If you distribute modified versions, you must also provide the corresponding source code
under the same GPL terms.
