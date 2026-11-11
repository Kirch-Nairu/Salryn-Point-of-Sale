# Salryn POS — Client Beta RC2 QA1: Fresh Windows Host Installation

**Distribution:** Link to the existing Google Drive installer; the executable is **not** stored in this GitHub repository or branch.

**Build identity:** `0.1.0-rc2.qa.1` · Windows x64 · Inno Setup package · QA-labeled client beta candidate.

**Installer (Google Drive):** [Salryn-POS-v0.1.0-rc2.qa.1-Win-x64-QA-Setup.exe](https://drive.google.com/file/d/1WCUleoDV1AWKtNX-ch7xzABVjmViCB6O/view?usp=drive_link)

| Package receipt | Expected value |
| --- | --- |
| File name | `Salryn-POS-v0.1.0-rc2.qa.1-Win-x64-QA-Setup.exe` |
| Size | `24,370,315` bytes |
| SHA-256 | `07bf3f4ca13aae8e2644b7ddee5b054f4294161929a2a4562f91f5922603acf9` |
| Application source | `ce554a2c354dce47952345862b3de59418a141de` |
| Packaging source | `556e04efee98d78816ab63bce0bef653d07f0e68` |
| Build UTC | `2026-11-11T07:30:26.3363903Z` |

The Google Drive file metadata lists the same file name and size. **That alone is not a SHA-256 verification of the downloadable bytes.** Verify the locally downloaded file against the checksum above before execution. The QA label in the filename is intentional: do not describe this build as a formally accepted final release.

## 1. What a clean Windows PC actually needs

| Item | Fresh-host requirement | Bundled / automatic? |
| --- | --- | --- |
| Operating system | **Windows 10 or Windows 11, 64-bit** according to the published Salryn hardware guide. Prefer a supported, updated Windows 11 host for a new deployment. | PC/OS supplied separately |
| CPU/architecture | **x64** PC; 64-bit Windows required. | No |
| .NET **10 Windows Desktop Runtime, x64** | **Required** by `Salryn.Desktop.WebView.exe` built for `net10.0-windows` and published **framework-dependent** (not self-contained). | **Not bundled** in the current setup; install if missing |
| .NET **10 ASP.NET Core Runtime, x64** | **Required** by `Salryn.Api.Local.exe` built for `net10.0` as an ASP.NET Core Web app, also published **framework-dependent**. | **Not bundled** in the current setup; install if missing |
| Microsoft Edge **WebView2 Evergreen Runtime** | **Required** for the desktop screen. It is **not** the same as needing to open the Edge browser. | Setup includes Microsoft's Evergreen **bootstrapper** and installs WebView2 if missing; this normally needs internet access |
| Administrator permission/UAC | **Required at installation** by the Inno Setup manifest. Normal operation uses the signed-in Windows account. | Windows permission, not a separate installer |
| Local application service | `Salryn.Api.Local.exe` automatically started by the desktop shell; it uses loopback `http://127.0.0.1:5299`. | Shipped with Salryn; no manual API launch required |
| SQLite database | Embedded local persistence under `C:\ProgramData\Salryn\data\salryn.db`; WAL/SHM sidecar files may be created. | Libraries shipped with the app; **no SQL Server, MySQL, SQLite server, or separate database installer** required |
| React frontend, DLLs and WebView2Loader | Prebuilt web assets `dist\`, API and shell files, SQLite dependencies and native WebView2 loader. | Shipped within the setup |
| Machine-bound Salryn license | An authorized license/activation provisioned for **this Windows host** and any licensed optional features. | **Not automatically granted** by downloading the installer |
| Local user/store setup | Operator credentials, store identity/configuration, products/opening stock and backup procedure. | Configured in Salryn after install |
| Internet | Needed to download the installer and **may be needed once** when WebView2 is absent. Licensing/provisioning connectivity depends on your approved activation route. Core single-host transactions are designed to operate locally afterward. | Connection supplied separately |
| Windows security/endpoint policy | Allow the trusted installed EXE and its child local API to execute; keep loopback available, and permit appropriate ProgramData writes. Do not broadly expose port 5299 to the LAN/internet. | Host configuration |
| Printing and POS peripherals | Optional **Windows-compatible receipt printer and installed driver** for physical printing, optional barcode scanner (keyboard-wedge), and optional cash drawer only with validated supported integration. | **Not supplied** with software |
| Data protection | Sufficient free disk space for app/runtimes, growing SQLite/WAL, receipts and backups; separate backup destination recommended. | Client/host responsibility |

**Critical packaging detail:** The current installer handles **WebView2** installation but does **not** automatically install the two .NET 10 runtimes. A completely fresh PC can therefore install the EXE successfully and still fail to launch the desktop or local API until the correct runtimes are installed.

### Official dependency downloads

- [.NET 10 downloads — Microsoft](https://dotnet.microsoft.com/en-us/download/dotnet/10.0): install **Windows Desktop Runtime 10.x x64** and **ASP.NET Core Runtime 10.x x64** if either is missing. These are **runtimes**, not the large .NET SDK; a developer SDK is unnecessary for clients.
- [WebView2 Runtime — Microsoft](https://developer.microsoft.com/en-us/microsoft-edge/webview2): the Salryn installer includes the online Evergreen Bootstrapper. For a completely disconnected fresh host, use Microsoft's **Evergreen Standalone Installer x64** beforehand rather than assuming the small bootstrapper works offline.

A matching `10.0.x` patch release of each .NET runtime is appropriate; a .NET 8 or 9 installation **alone** is not an alternative to the target .NET 10 runtime.

### Recommended client host hardware (guidance, not installer-enforced minimums)

| Deployment | CPU | Memory | Storage | Display |
| --- | --- | --- | --- | --- |
| One Windows POS host | Intel Core i3 6th generation or newer | 8 GB RAM | 128 GB SSD | 1366 × 768 or better |
| Host with approved extra terminals | Intel Core i5 8th generation or newer | 16 GB RAM | 256 GB SSD | Suitable Windows desktop display |

A stable power source is important; a UPS is recommended for stores. Additional-terminal capability is deployment-specific and **not** implied merely by installing this single-host beta.

## 2. Fresh-host checklist

- [ ] Confirm Windows x64 and adequate disk space; apply ordinary Windows security updates.
- [ ] Install **.NET 10 Windows Desktop Runtime x64** and **.NET 10 ASP.NET Core Runtime x64** from Microsoft, unless already present.
- [ ] Provide internet during setup if WebView2 Evergreen Runtime is missing, or preinstall Microsoft's x64 offline standalone WebView2 runtime.
- [ ] Obtain the exact versioned EXE from the **Google Drive link above**.
- [ ] Verify downloaded **SHA-256** matches the package receipt. **Do not run a mismatch.**
- [ ] Close other Salryn windows if this is an upgrade; on a truly fresh machine skip this step.
- [ ] Run the EXE and accept the standard Windows administrator/UAC prompt. Use the default installation directory unless there is a known reason to change it.
- [ ] Let the bundled setup install WebView2 if needed; launch **Salryn POS** using the desktop or Start Menu shortcut.
- [ ] Supply authorized host-specific license, then complete store/operator setup. Do **not** reset an existing license or production database to bypass activation.
- [ ] Confirm application UI opens, local API starts, sign-in works, product screens load, database/config are writable, and the app survives close/reopen.
- [ ] Set a backup destination and verify a non-destructive backup/recovery procedure before pilot transactions.
- [ ] Only if physical printing is needed: install the approved Windows printer driver, select printer in Salryn settings, and test against the actual model.

## 3. Optional read-only PowerShell dependency checks

These commands inspect Windows and the downloaded installer. They **do not install** software, reset the database, or change licensing.

```powershell
# Windows OS and architecture
Get-CimInstance Win32_OperatingSystem |
  Select-Object Caption, Version, OSArchitecture

# .NET 10 runtime presence — both families must be listed
if (Get-Command dotnet -ErrorAction SilentlyContinue) {
  dotnet --list-runtimes
} else {
  Write-Host 'dotnet not found: install .NET 10 Windows Desktop and ASP.NET Core runtimes (x64).'
}

# Change this path to where you saved the EXE
$setup = Join-Path $env:USERPROFILE 'Downloads\Salryn-POS-v0.1.0-rc2.qa.1-Win-x64-QA-Setup.exe'
$expectedSha256 = '07bf3f4ca13aae8e2644b7ddee5b054f4294161929a2a4562f91f5922603acf9'
if (Test-Path -LiteralPath $setup -PathType Leaf) {
  $actual = (Get-FileHash -LiteralPath $setup -Algorithm SHA256).Hash.ToLowerInvariant()
  [PSCustomObject]@{
    Path = $setup
    Bytes = (Get-Item -LiteralPath $setup).Length
    SHA256 = $actual
    MatchesExpected = ($actual -eq $expectedSha256)
  } | Format-List
} else {
  Write-Host "Installer is not in the assumed Downloads path: $setup"
}
```

**Expected .NET family names** in the runtime output: `Microsoft.WindowsDesktop.App 10.0.x` and `Microsoft.AspNetCore.App 10.0.x`. `Microsoft.NETCore.App 10.0.x` is typically installed as a common dependency as well.

After Salryn has launched, these are optional **read-only** checks for the installed local service:

```powershell
Test-NetConnection 127.0.0.1 -Port 5299
Invoke-WebRequest 'http://127.0.0.1:5299/health' -UseBasicParsing |
  Select-Object StatusCode
Test-Path 'C:\ProgramData\Salryn\data\salryn.db'
```

A healthy API is expected at loopback port 5299 **while Salryn is running**. Do not change the host firewall to expose this port externally just to make the single-host app work.

## 4. Installed files, database and upgrade behavior

- Default program directory: `C:\Program Files\Kirjane Information Technology Services\Salryn POS` (unless Setup reuses a prior selected install directory).
- Main desktop executable: `Salryn.Desktop.WebView.exe`; local API executable: `Salryn.Api.Local.exe`. The user launches **Salryn POS**, not the API separately.
- Shared operational directory: `C:\ProgramData\Salryn` with `data`, `config`, `backups`, `exports`, `logs`, and `receipts`; machine-bound license/recovery material may also live under the shared data tree.
- The Inno Setup script grants standard users **Modify** on these Salryn ProgramData folders so SQLite can write its database and WAL/SHM files after a non-admin launch.
- The installer keeps a **stable AppId** across versions and replaces installed application files on upgrade; it is deliberately written to **preserve ProgramData** on upgrade or uninstall. Do not delete ProgramData to "refresh" an app or license.
- For existing stores, arrange a consistent database/config/license backup before version upgrades; a mere executable file copy may not roll back database-schema changes.

## 5. Peripherals and features that are **not** fresh-install dependencies

The following are **not needed to start the standard single-host UI**: Node.js, npm, Git, PowerShell scripts, Inno Setup compiler, .NET SDK, Visual Studio, Docker, Windows Sandbox, Hyper-V, IIS, SQL Server, MySQL, external cloud server, networked cash drawer, barcode scanner, thermal printer, or a second Windows user profile.

Printer/scanner/cash-drawer hardware, supported drivers and terminal configurations are optional by deployment. For the current QA1-derived beta, **PR/PO/RR/quotation/voucher printable document presentation and genuine PDF output remain under corrective review**; installing the client beta does not prove those outputs are production-ready.

## 6. Common first-start failures

| Symptom | Verify first |
| --- | --- |
| Application says it needs a .NET runtime / no app window | Missing **.NET 10 Windows Desktop Runtime x64** (or wrong architecture). |
| UI opens but local service does not start, localhost health fails | Missing **.NET 10 ASP.NET Core Runtime x64**; API executable, port 5299 conflict, endpoint security, or permissions. |
| WebView2 runtime missing / blank app window | Microsoft Edge WebView2 Evergreen Runtime; online bootstrapper network failure; retry after installing runtime. |
| Cannot create/update database or SQLite readonly error | Permissions to `C:\ProgramData\Salryn` and WAL/SHM files; never delete the DB as a workaround. |
| License appears unactivated or incorrect after transfer | Installation/machine identity, entitled features, and authorized license provisioning. A license from another PC may not be valid. |
| Printer produces wrong content/layout or no PDF | Capture the document button, preview/printed output and driver selection; PDF/document output is tracked as an unresolved beta defect, not a missing .NET package. |
| Package hash differs | Stop and re-download from the exact distribution link; request a confirmed updated checksum rather than accepting mismatched bytes. |

## 7. Release and support notes

This page documents the user-supplied **Google Drive client-beta installer link** and the fresh-host dependencies verified from its packaging source. It does not upload, mirror, repackage, rebrand, sign or redistribute the executable through GitHub; the download remains hosted in Drive.

**Status:** QA-labeled beta candidate, not a claim of full financial/printing acceptance or a final production release. Features such as customer quotations, procurement, payables, receivables, document printing and other new workflows still have separately tracked audit findings; acceptance for a particular client must be performed against the actual build.

**Support:** `salryn.info@gmail.com`

**Publisher:** Kirjane Information Technology Services / Kirjane Labs.
