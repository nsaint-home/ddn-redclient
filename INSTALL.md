# Install DDN-RedClient

**Version 3.2.3** · 2026-10-22 · see [CHANGELOG.md](CHANGELOG.md)

Download packages from this repository’s **Releases** page. Pick the asset that matches your OS. Packages built for one OS do not run on another.

Account data is **not** inside the app install. Upgrades leave saved accounts in place:

| OS | Account location |
| --- | --- |
| Linux / macOS | `~/.config/ddn-redclient/` |
| Windows | `%LOCALAPPDATA%\ddn-redclient\` |

## Which file to download

| Your system | Release asset |
| --- | --- |
| Debian / Ubuntu | `ddn-redclient_<version>_amd64.deb` |
| RHEL / Rocky / similar | `ddn-redclient-<version>-1.*.x86_64.rpm` |
| Linux (no root) | `redclient_<version>_linux-x86_64.zip` (or `linux-aarch64`) |
| macOS | `redclient_<version>_macos-arm64.zip` or `macos-x86_64.zip` |
| Windows | `redclient_<version>_windows-amd64.zip` |

Replace `<version>` with the release version (for this tree, `3.2.3`).

## Debian / Ubuntu

```bash
sudo apt install ./ddn-redclient_3.2.3_amd64.deb
redclient --version
```

Upgrade by installing a newer `.deb` the same way.

## RHEL / Rocky

```bash
sudo dnf install ./ddn-redclient-3.2.3-1.x86_64.rpm
redclient --version
```

Upgrade by installing a newer `.rpm` the same way.

## Linux or macOS (zip)

Unzip the release archive, then run the bundled installer (see `INSTALL.txt` inside the zip):

```bash
./install.sh ./redclient_3.2.3
source ~/.bashrc   # or open a new terminal
redclient --version
```

On macOS the zip may also include `RedClient.app`. Re-run `install.sh` with a newer unzipped folder to upgrade.

## Windows (zip)

Unzip the release archive, then in PowerShell:

```powershell
.\install.ps1 -Source .\redclient_3.2.3
redclient --version
```

Re-run `install.ps1` with a newer unzipped folder to upgrade. Open a new terminal if `redclient` is not on `PATH` yet.

## After install

- CLI: `redclient …` (some installs also provide `redc`)
- Desktop: `redclient gui`

For product overview and security contacts, see [README.md](README.md) and [SECURITY.md](SECURITY.md).
