# Install DDN-RedClient

**Version 4.0.0** · 2026-10-30 · see [CHANGELOG.md](CHANGELOG.md)

Download packages from this repository’s **Releases** page. Pick the asset that matches your OS. Packages built for one OS do not run on another.

**Artifact names** use the prefix `red-demo-client` so they are not confused with DDN’s POSIX **red-client**. The CLI commands remain `redclient` and `redc`.

Account data is **not** inside the app install. Upgrades leave saved accounts in place:

| OS | Account location |
| --- | --- |
| Linux / macOS | `~/.config/ddn-redclient/` |
| Windows | `%LOCALAPPDATA%\ddn-redclient\` |

## Supported platforms

| Release asset | Architecture | Minimum host |
| --- | --- | --- |
| `red-demo-client_*_amd64.deb` / `red-demo-client_*_linux-x86_64.zip` | x86_64 | **Ubuntu 24.04+** or **Debian 12+** (**glibc ≥ 2.39** when frozen on Ubuntu 24.04) |
| `red-demo-client_*_arm64.deb` / `red-demo-client_*_linux-aarch64.zip` | aarch64 | Same glibc rule on ARM64 Linux |
| `red-demo-client-*.x86_64.rpm` / `*.aarch64.rpm` | matching RPM arch | **RHEL / Rocky 9+** when the RPM was frozen on that baseline |
| `red-demo-client_*_macos-arm64.zip` / `macos-x86_64.zip` | arm64 / x86_64 | macOS matching the freeze CPU |
| `red-demo-client_*_windows-amd64.zip` | amd64 | 64-bit Windows |

**Linux constraint is glibc, not kernel.** Packaged builds bundle libraries from the freeze host; the install host needs a glibc at least as new as that freeze. Building on a newer Ubuntu than you support (for example glibc 2.43) breaks Ubuntu 24.04 clients.

Each Linux zip’s `INSTALL.txt`, the `.deb` / `.rpm` description, and `redclient help platforms` show the **exact** glibc stamp for that artifact. Prefer freezing release packages on **Ubuntu 24.04**.

Desktop GUI: **X11** and **Wayland** are both supported once the package matches the host.

After install, confirm:

```bash
ldd --version
redclient help platforms
redclient --version
```

## Which file to download

| Your system | Release asset |
| --- | --- |
| Debian / Ubuntu | `red-demo-client_<version>_amd64.deb` (or `_arm64.deb`) |
| RHEL / Rocky / similar | `red-demo-client-<version>-1.*.x86_64.rpm` (or `aarch64`) |
| Linux (no root) | `red-demo-client_<version>_linux-x86_64.zip` (or `linux-aarch64`) |
| macOS | `red-demo-client_<version>_macos-arm64.zip` or `macos-x86_64.zip` |
| Windows | `red-demo-client_<version>_windows-amd64.zip` |

Replace `<version>` with the release version (for this tree, `4.0.0`).

## Debian / Ubuntu

```bash
sudo apt install ./red-demo-client_4.0.0_amd64.deb
redclient --version
redclient help platforms
```

If an older `ddn-redclient` package is installed, remove it first (or let apt apply the Conflicts/Replaces from this package).

Upgrade by installing a newer `.deb` the same way.

## RHEL / Rocky

```bash
sudo dnf install ./red-demo-client-4.0.0-1.x86_64.rpm
redclient --version
redclient help platforms
```

Upgrade by installing a newer `.rpm` the same way.

## Linux or macOS (zip)

Unzip the release archive, then run the bundled installer (see `INSTALL.txt` inside the zip):

```bash
./install.sh ./red-demo-client_4.0.0
source ~/.bashrc   # or open a new terminal
redclient --version
```

On macOS the zip may also include `RedClient.app`. Re-run `install.sh` with a newer unzipped folder to upgrade.

## Windows (zip)

Unzip the release archive, then in PowerShell:

```powershell
.\install.ps1 -Source .\red-demo-client_4.0.0
redclient --version
```

Re-run `install.ps1` with a newer unzipped folder to upgrade. Open a new terminal if `redclient` is not on `PATH` yet.

## After install

- CLI: `redclient …` (some installs also provide `redc`)
- Desktop: `redclient gui` (PySide6 is bundled in packaged builds)
- Platform check: `redclient help platforms`

For product overview and security contacts, see [README.md](README.md) and [SECURITY.md](SECURITY.md).
