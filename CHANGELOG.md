# Changelog

## 3.2.8 — 2026-10-29

### tags find (large buckets)
- Pipelined list→tag: listing fills a bounded queue while workers run concurrent `GetObjectTagging` (no full target list in RAM)
- `--queue-size N` (default 2048), `--spill [PATH]` (JSONL matches on disk), `-o text` skips collecting all rows
- Progress shows `scanned` / `matched` / `listed` / `queue`; help + User Manual updated
- Fix: `--progress` no longer deadlocks after ~100 listed keys (non-reentrant lock nested in progress print)

## 3.2.7 — 2026-10-29

### Desktop
- Provision / Teardown progress is a free-floating window (move, resize, minimize/maximize); the main app stays usable. Close stays blocked until the job finishes. Other dialogs remain modal.
- Browse Tags dialog: vertical splitter between AWS-compliant and DDN-unlimited sections (drag to give DDN more room)

## 3.2.6 — 2026-10-28

### S3 tags / browse filters
- `redclient tags find -b BUCKET [-p PREFIX] --match FILTER` scans a bucket/prefix with concurrent GetObjectTagging (current object by default; `--all-versions` for every VersionId). Repeat `--match` for AND; `,` / `|` inside one filter for OR. `--workers`, `--max-keys`, `--progress`, `--no-unlimited`; `-o table|json|text`
- `redclient tags get|set|delete` honor `-o table|json|text` (Key/Value table by default on get)
- Browse listing **Name**, **Lock**, and **Tags** filters accept multiple alternatives with `,` or `|` (OR), e.g. `*.docx, *.pdf` or `Environment=prod | Team=ops`
- Tenants → **Create Bucket** / `create_tenant_bucket` stamps the same `Tenant` / `ManagedBy=redclient` and `ddn:provision.*` tags as full provision (soft-fail if tagging fails)

### Docs
- Help (`tags` cookbook for get/set/delete/find, `s3 ls`, `loadgen`, `gui`, `help start` / printable manual), User Manual (Browse Tags + CLI find), DEVELOPMENT command tree, and packaging version sync

## 3.2.5 — 2026-10-27

### S3 tags (Infinia 2.5+ DDN-unlimited)
- New `ddn_redclient/s3/tagging.py` helper: put/get/delete object and bucket tags, `x-ddn-tagging-*` header injection, pagination, and AWS vs `ddn:` split
- `redclient tags get|set|delete` and `redclient s3api` tagging operations (`get/put/delete-object-tagging`, bucket equivalents) with `--unlimited`, `--tag`, `--tag-prefix`, `--max-tags`, `--ddn-prefix`
- Load gen stamps `ddn:loadgen.*` after create/modify by default; each object version adds `ddn:loadgen.vN` and updates `ddn:loadgen.version`; `--tag`, `--no-loadgen-tags`, `--cleanup-loadgen-tags`; desktop Load gen rail checkbox and Extra tags field
- S3 Browse **Selection → Tags…** / **Bucket → Tags…** (and context menu) edit AWS-compliant and DDN-unlimited tags
- S3 Browse listing **Tags** filter (`key`, `key=value`, `ddn:loadgen.v*`, `*=value`) loads object tags for the open folder and hides non-matches (folders stay)
- S3 provision stamps the default bucket with `Tenant` / `ManagedBy=redclient` plus `ddn:provision.*` tags (tenant, subtenant, service, admin, created, object_lock)
- Applying or removing a bucket policy stamps `ddn:policy.*` tags (enabled, updated, statement_count, sids, public, effects, summary) without replacing AWS tags

### Docs
- Help (`tags`, `s3api`, `loadgen`, `provision`, `gui`), User Manual (Browse Tags dialog + filter, provision stamps, loadgen stamps), README / INSTALL / DEVELOPMENT version stamps, and packaging sync cover the tagging release

## 3.2.4 — 2026-10-27

### Provision / quotas
- S3 provision applies the subtenant quota as the default bucket's allocated capacity (`bulk_quota` / `redcli s3 bucket … -A`) so Services, `redcli dataset list` / `s3 bucket list`, and the provisioned ceiling stay aligned
- POSIX still maps the subtenant quota to service `datasetsize` (unchanged)
- `redclient service-quota` and Management → Services → **Edit quota** resize the subtenant ceiling and the matching POSIX dataset size or S3 bucket capacities (cannot exceed the tenant quota)
- Quota editors use amount + **MB / GB / TB / PB** (decimal SI → bytes for the API, same as `redcli dataset list`); bare `10GB` / `50GB` are 10⁹-based, `GiB` / `MiB` stay binary
- Services / Monitor capacity and usage labels match `redcli` (`126.9 GB`, `3.101 GB (2%)`, `0 B (0%)`)

### Desktop
- Title bar: DDN logo on the left; operating-mode badge, theme toggle, and connectivity badge on the right show **Connected · \<Infinia version\>** or **Offline** (`GET /redapi/v1/version`). Offline tooltip says Click to connect; click the badge to refresh. Saving a realm account re-probes automatically

### Docs
- Help (`provision`, `service-quota`, `gui`), User Manual (Management / Getting started), README / INSTALL / DEVELOPMENT version stamps, and packaging sync cover the quota and connectivity changes

## 3.2.3 — 2026-10-22

### Packaging
- Windows `install.ps1` prints progress before copying the ~600 MiB onedir, uses `robocopy` when available, and accepts the outer zip folder (or a nested `redclient_*` onedir) as `-Source`. Passing a `.zip` path fails with an unzip hint instead of hanging on a silent copy
- PowerShell profile completion uses `Invoke-Expression (redclient completion powershell | Out-String)`. Piping the script into `Invoke-Expression` without `Out-String` ran each line alone and failed with empty-string / missing `}` errors

## 3.2.2 — 2026-10-22

### Packaging / maintenance
- Product version is hand-edited only in `project.json`. `packaging/sync_version.py` updates `_version.py`, `pyproject.toml`, the README version line, and the packaging README bump sentences; `packaging/build.py` runs that sync before a freeze
- GitHub freeze smoke checks use `packaging/build.py --print-ship-dir` so paths match `redclient_<version>_<os>-<arch>/`
- A misnamed root `redclient/` venv is gitignored; use `.venv` / `.venv-freeze`

## 3.2.1 — 2026-10-21

### Provision
- The tenant wizard and `redclient provision` accept protocol `posix` as well as `s3`. POSIX asks for a dataset and a mount point. It does not create a bucket, vhost, hosts line, or S3 key. S3 is unchanged
- POSIX teardown skips bucket purge and the vhost hosts line. If the mount point is mounted on this machine it is unmounted first, then the service and dataset are deleted
- After a POSIX provision, the dialog and CLI say the Linux mount node still needs the redfs client (`redcli`, `red-client-common`, `red-client-fs`, `red-client-fs-dkms`) and print the server, tenant, subtenant, admin user, service, dataset, mount point, and the `redcli client setup` and `mount -t redfs` commands

### S3 Browse
- The long action row is a short file bar (Up, Upload, Download, New Folder, Delete) plus **Selection** and **Bucket** menus. Selection holds Rename, Restore, Presign, Object Lock, and Trim. Bucket holds Versioning, Policy, and Lock defaults
- User Manual, Getting started, `redclient help`, and the README describe that toolbar and include screenshots of the bar and both menus

### Provision / teardown
- Provision and Teardown open a step dialog that lists each phase as it runs (tenant, service, hosts, keys, bucket, validate, and the matching teardown steps)
- Those jobs turn off the soft “slow cluster connection” health offer so the timeout prompt does not interrupt a long provision or teardown
- User Manual, Getting started, `redclient help` (provision, teardown, gui), the Tenants page note, and the README describe that dialog and include screenshots

## 3.1.48 — 2026-10-21

### Monitoring
- Before SSH, Containers and the other node checks try the SSH port for 3s. An address that does not answer fails then, and SSH is not started. When the port answers, the handshake may take 45s, and Containers still allows 120s so `docker ps` has time after a slow login

## 3.1.47 — 2026-10-21

### CLI
- `redclient completion powershell` prints a PowerShell completer for `redclient` and `redc`. `packaging/install.ps1` adds `redclient completion powershell | Invoke-Expression` to the Windows PowerShell 5.1 and PowerShell 7 profiles

## 3.1.46 — 2026-10-20

### Accounts / hosts
- Windows restore and `account sync-hosts` ask for administrator permission (UAC) before writing the hosts file, and write every `{tenant}.s3` line in that one prompt
- When the hosts file is not updated, or the S3 port does not answer, the GUI shows a dialog and the CLI prints the same note: run `redclient account sync-hosts` (on Windows, from an elevated PowerShell if the prompt did not appear)

## 3.1.45 — 2026-10-20

### Desktop
- Windows GUI startup no longer crashes the Task Scheduler reader threads. `schtasks` output is decoded with the console code page, and a byte cp1252 cannot represent is kept as a replacement character

## 3.1.44 — 2026-10-19

### Desktop
- S3 Browse → Refresh reloads the open left-hand folders. Deleting a folder no longer leaves that folder in the navigation pane when the bucket list itself is unchanged

## 3.1.43 — 2026-10-19

### Desktop
- Light mode selection is a grey shadow with dark text, including the left rail, Browse sidebar, and Tools list. The active rail item keeps the red left edge

## 3.1.42 — 2026-10-19

### Desktop
- The rail log is a file, `redclient.log` in the config directory, opened in the system text editor (Notepad, TextEdit, or the Linux default). Debug log in Management → Settings records RED API, S3, and redcli calls there. Passwords, tokens, and request bodies are left out. `REDCLIENT_DEBUG_LOG=1` forces it on

## 3.1.41 — 2026-10-19

### CLI
- Flags accept a single dash as well as the double-dash form. `-t`, `-tenant`, and `--tenant` are the same. A one-letter flag is added when that letter is free on the command; if the letter is already used, pass the full name. Help examples and the README use the single-dash form

## 3.1.40 — 2026-10-16

### Desktop
- Tools → Troubleshoot shows each node's command output in the lower pane. A containers-only run lists `docker ps -a` there, and the matrix stays visible when every node matches

## 3.1.39 — 2026-10-16

### CLI
- Troubleshooting is `redclient troubleshoot`. It is no longer a `monitor` view. The Tools rail page is unchanged. `--check cluster-health` still adds `redcli cluster show -H`

## 3.1.38 — 2026-10-16

### Desktop
- Troubleshoot is the first entry on the Tools rail. Monitoring keeps Status through Containers and Grafana. CLI stays `redclient monitor troubleshoot`

## 3.1.37 — 2026-10-16

### Desktop
- Monitoring → Troubleshoot paints the lower pane with the same transcript used by Tools, so `redcli` color codes stay out of the column width. Wide tables stay on one line and scroll sideways

## 3.1.36 — 2026-10-16

### Desktop
- Monitoring → Troubleshoot: drag the bar between the matrix and the transcript to resize both. The transcript paints `redcli` colors and keeps each table row on one line, so the box columns line up

## 3.1.35 — 2026-10-16

### Desktop
- Monitoring → Troubleshoot runs the on-node checks over the saved SSH login. Results that span every node use the same name × node matrix as Containers. `redcli` realm and event commands run once on the saved host, each under a header, and one failure does not stop the rest
- CLI: `redclient monitor troubleshoot`. `--check cluster-health` adds `redcli cluster show -H`

## 3.1.34 — 2026-10-14

### Desktop
- S3 Browse has a User menu when a tenant has more than one saved key. Auto still uses the provision key. Pick another user to sign listings, uploads, and reports as that user

## 3.1.33 — 2026-10-14

### CLI
- `redclient lock` removes the login-session password so the next command asks for the realm admin password again. Closing the app and logging out are unchanged

## 3.1.32 — 2026-10-14

### Desktop
- A new tenant access key is valid for 2 years (`2y`). Provision and Access → Create show the service, that term, the access key, and the secret once so they can be copied. CLI `provision` and `access create` print the same fields

## 3.1.31 — 2026-10-14

### Packaging
- The onedir and the zip you copy to another host are named `redclient_<version>_<os>-<arch>` (for example `linux-x86_64`, `macos-arm64`, `windows-amd64`)
- That zip also contains `INSTALL.txt` and `install.sh` or `install.ps1`, so the extracted folder is the install package. The macOS zip includes `RedClient.app`

### Desktop
- User Manual drawings for Management → Settings and Load gen match the current screens: Settings shows operating mode, realm, backup/restore, SSH, Grafana, flags, multipart, and trim jobs; Load gen shows Account, multipart, full-width progress, and the status-log search bar
- The Operating mode help picture loads again. S3 Browse help now includes the Trim, Policy, Object Lock, and Lock defaults dialogs

## 3.1.30 — 2026-10-14

### Desktop
- Import and Store secret keep one account per tenant and username (`tenant/username`). The same username on another tenant keeps its own password, access key, and secret instead of overwriting the first user

## 3.1.29 — 2026-10-13

### Desktop
- Account restore on macOS asks with the administrator dialog when adding `{tenant}.s3` to `/etc/hosts`. The previous `sudo` call had no terminal in the app, so that step failed or hung after the accounts file had already unlocked

## 3.1.28 — 2026-10-13

### Desktop
- Management API calls are capped at 2 per RedClient and 4 shared by every RedClient on the same computer, so a second window does not start another full tenant walk
- Tenant-user logins and the tenant list are reused across account/tenant switches instead of signing in again for every probe
- If this app's own calls pile up or stay stuck, it closes them and drops queued refreshes. A health summary is not added while that budget is already in use

## 3.1.27 — 2026-10-12

### Packaging
- Freeze switches from PyInstaller **onefile** to **onedir** (launcher + ``_internal/``); same freeze-src → per-OS build → ``.deb``/``.rpm`` / ``install.sh`` / ``install.ps1`` channels; shippable ``dist/redclient_<version>/`` + ``.zip``

## 3.1.26 — 2026-10-12

### Docs
- Help and User Manual validated against current surface: Monitoring Containers / no Performance tab, report ``http://127.0.0.1`` opens, Provision Name/Email/Groups; index + ``help gui`` / ``help report`` notes aligned

## 3.1.25 — 2026-10-12

### Desktop
- GUI startup no longer creates Qt WebEngine until a report HTML view is opened; Chromium uses a home cache (``~/.cache/ddn-redclient/qtwebengine``) to avoid ``/tmp`` puffin mmap failures that could abort ``redclient gui``

## 3.1.24 — 2026-10-12

### Management
- Provision and Add user accept optional Name, Email, and Groups (GUI + CLI ``--admin-name`` / ``--name``, ``--admin-email`` / ``--email``, ``--admin-groups`` / ``--groups``); applied via ``PUT /redapi/v1/user``

## 3.1.23 — 2026-10-12

### Fix
- Report pack server serves CSV/JSON/Markdown as inline ``text/plain`` / JSON so the browser shows them instead of a Save dialog

## 3.1.22 — 2026-10-12

### Fix
- Reports open via ``http://127.0.0.1`` (local pack server) so snap Brave/Chromium can follow CSV/JSON/MD links — ``file://`` under ``~/.local/share`` is blocked and showed ``ERR_FILE_NOT_FOUND``

## 3.1.21 — 2026-10-12

### Fix
- Report HTML no longer links pack files that were not written (e.g. ``compliance_keys_on_hold.csv`` when Object Lock is off) — that caused browser ``ERR_FILE_NOT_FOUND``

## 3.1.20 — 2026-10-12

### Desktop
- Reports: **Open externally** and pack CSV/JSON/Markdown links open in the default web browser (``file://``), not a spreadsheet/OS handler

## 3.1.19 — 2026-10-12

### Desktop
- Reports viewer: CSV/JSON/Markdown links in ``REPORT.html`` are handled instead of dropped as unhandled WebEngine downloads

## 3.1.18 — 2026-10-12

### Desktop
- Monitoring rail drops the Performance tab (use Grafana for metrics); ``redclient monitor performance`` stays on the CLI

## 3.1.17 — 2026-10-12

### Monitoring
- Containers view compares services by name across nodes (matrix + differences-only); flags missing / image / state skew
- CLI ``monitor containers`` prints the compare matrix first (exit 1 when skew); per-node lists follow

## 3.1.16 — 2026-10-12

### Desktop / Packaging
- Frozen binary and GUI window use the DDN logo as the application icon (``app-icon.png``, ``redclient.ico``; macOS ``.icns`` when ``iconutil`` is available)
- ``packaging/prepare_icons.py`` runs from ``packaging/build.py`` before PyInstaller

### Monitoring
- New **Containers** view (CLI ``redclient monitor containers``): ``docker ps -a`` on each inventory node over the saved realm SSH login

## 3.1.15 — 2026-10-12

### Desktop / CLI
- Slow cluster connections offer a Monitoring summary health check — GUI dialog or CLI TTY prompt; non-TTY prints a hint for ``redclient monitor summary``
- Soft wait is about 15s (20s in the frozen onefile binary); override with ``REDCLIENT_HEALTH_OFFER_SECONDS``
- Reuses ``collect("summary")`` (same as Monitoring → Summary); cooldown avoids spam; diagnostic itself is time-bounded

## 3.1.14 — 2026-10-12

### Fix
- Inventory: allow stale disk cache so S3 Browse paints when the cluster is slow, then revalidate
- Cache ``GET /tenants`` for primary-admin lookups (was re-fetched per tenant, ~10s each)
- ``s3 ls --bucket`` resolves owner from inventory cache instead of walking every tenant live
- Probe tenants in parallel for live inventory; MainWindow close no longer waits forever on hung jobs
- S3 endpoint prefers ``{tenant}.s3`` hosts aliases before slow ``/s3/config``; shorter RED/S3 timeouts so a sick cluster fails fast instead of wedging the GUI/CLI

## 3.1.13 — 2026-10-11

### Desktop
- Shell theme aligned with Infinia Console: slate dark/light palette, left rail with icon+label planes and brand-red active accent; action blue for buttons and list selection
- Fix background jobs dropping results (``RuntimeError: Signal source has been deleted``) so S3 Browse / inventory callbacks reach the UI again
- S3 Browse / Load gen / Reports: do not clear a non-empty bucket list when the S3-protocol tenant probe is empty

### Docs
- User Manual figures, CSS, HTML captions, ``help gui``, and README updated for Console-style rail and palette

## 3.1.12 — 2026-10-11

### Desktop
- Title-bar logo uses the primary-color DDN mark (transparent PNG); red ring stays the same in light and dark, wordmark / “Demo App” follow the theme
- Management → Settings → Trim jobs: Add / Edit / Remove (was read-only)

### Accounts / hosts / lifecycle
- Trim jobs are cross-platform: crontab on Linux/macOS, Windows Task Scheduler (``schtasks``) with ``.cmd`` wrappers

### Docs
- Help topics for ``account backup`` / ``restore`` / ``sync-hosts``; ``help start`` and printable manual cover moving credentials
- User Manual (Getting started + Management) and figures for Settings Backup/Restore, SSH password, hosts sync, Users/Access info icons
- Help + User Manual for Trim jobs Add/Edit/Remove; crontab (Linux/macOS) and Windows Task Scheduler; ``help start`` / printable Getting started

## 3.1.11 — 2026-10-11

### Desktop
- Management → Settings: **Backup accounts…** / **Restore accounts…** for the encrypted store (Salted__; same realm admin password on restore)

## 3.1.10 — 2026-10-11

### Accounts / hosts
- System hosts aliases work on Linux, macOS (``/etc/hosts``), and Windows (``%SystemRoot%\\System32\\drivers\\etc\\hosts``), with UAC elevation on Windows
- ``redclient account restore`` checks/adds ``{tenant}.s3`` hosts lines for restored tenants; ``redclient account sync-hosts`` re-runs that check anytime

## 3.1.9 — 2026-10-11

### Desktop
- Main window is an explicitly resizable / maximizable desktop window (min 720×480)
- Management → Settings scrolls vertically and horizontally when the window is short or narrow

## 3.1.8 — 2026-10-11

### Desktop
- Management → Settings: **SSH password** field (with host + user); key path optional when using password

## 3.1.7 — 2026-10-10

### Desktop / accounts
- ``redclient account backup`` / ``restore`` to move the encrypted accounts file between machines (freeze-src zip does not include credentials)
- Windows config dir defaults to ``%LOCALAPPDATA%\\ddn-redclient`` (keeps legacy ``~\\.config\\ddn-redclient`` if that store already exists)
- Settings shows the accounts file path and how to migrate imported users / S3 secrets
- Packaging docs distinguish first-time freeze vs rebuild, and moving credentials vs source

## 3.1.6 — 2026-10-10

### Desktop
- Management → Users / Access spell out the S3 secret model: cluster shows access keys but never the secret after Create; a compact info icon (check when ready) shows the next action on hover or click
- Users pre-fills the live access key on Import when the user has exactly one key

## 3.1.5 — 2026-10-10

### Tools / CLI
- ``redclient -rt`` / Tools rail run ``redcli user login`` on the node with the saved realm account before tools that need a cluster session (fixes ``rt_health`` “No active redcli session”)

## 3.1.4 — 2026-10-10

### Desktop
- Faster S3 Browse in the frozen binary: reuse realm login, soft inventory prefetch, keep BrowseCache/selection on refresh, skip versioning/lock on sidebar/child prefetch, warm boto3 early when frozen

## 3.1.3 — 2026-10-10

### Desktop
- Title bar wordmark reads **ddn - Demo App** (normal weight beside the DDN logo)

## 3.1.2 — 2026-10-09

### Desktop / packaging
- **Users → Import…** accepts optional S3 access key + secret so a new client can Browse tenant buckets in one step (password alone is not enough)
- Access → Create… auto-stores the secret when that user is already imported
- Frozen binary + `.deb` / `.rpm` packaging (`packaging/`); ship `redclient_<version>` and install as plain `redclient` / `redc`

## 3.1.1 — 2026-10-09

### Desktop
- **Operating mode** — SuperAdminUser vs Tenant-scoped demo isolation (title-bar badge + Settings); Monitoring/Tools restricted in Tenant mode; badge shows `Tenant Mode: <tenant> / <admin>`
- GUI opens on **S3 Browse** when a realm account is saved (first-run still lands on Settings)
- **Shared inventory cache** (`inventory-cache.json`) for buckets/S3 tenants — GUI prefetch, Browse/Load gen/Reports/Management Tenants, and CLI `s3 buckets` share it; lazy S3 warm on first bucket open
- Help `-h` is **cache-only** for example names (aws-cli.sh style); live probes no longer block help

### CLI / library
- Clear error when `--bucket` is omitted (`s3 stats`, etc.) instead of a traceback
- `list_live_buckets` refreshes the shared inventory cache; help examples can use that cache

### Docs
- User Manual + figures for operating mode; README/help notes for inventory cache and mode
