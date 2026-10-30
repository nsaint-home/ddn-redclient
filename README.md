# DDN-RedClient

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

**Version 4.0.0** · 2026-10-30 · **Phase 3** · see [CHANGELOG.md](CHANGELOG.md)

Portable client for **DDN Infinia**: realm administration, S3 browse and transfer, cluster monitoring, and S3 load generation — as a CLI (`redclient`) and a desktop window (`redclient gui`).

## Download

Installers for Linux, Windows, and macOS are published on this repository’s **Releases** page (not in the git tree).

| Platform | Typical assets |
| --- | --- |
| Debian / Ubuntu | `red-demo-client_*.deb` (Ubuntu **24.04+** / Debian **12+** when frozen on 24.04) |
| RHEL / Rocky | `red-demo-client-*.rpm` (RHEL/Rocky **9+** when frozen on that baseline) |
| Linux / macOS / Windows | platform zip (`red-demo-client_<version>_<os>-<arch>.zip`) |

Linux packages need **glibc ≥ the freeze host** (not a specific kernel). See **[INSTALL.md](INSTALL.md)** for the full matrix, which file to pick, and how to install or upgrade. After install: `redclient help platforms`.

## License

This project is licensed under the [MIT License](LICENSE).

## Security

To report a vulnerability, see **[SECURITY.md](SECURITY.md)**.

**Maintainer:** Norman St.Pierre — nstpierre@ddn.com · norman.stpierre@gmail.com

## Changelog

Release history: [CHANGELOG.md](CHANGELOG.md).
