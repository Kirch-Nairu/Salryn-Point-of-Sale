# Salryn POS `0.1.0-rc2.qa.1` — Fresh Windows Host Dependencies

**Distribution identity:** `Salryn-POS-v0.1.0-rc2.qa.1-Win-x64-QA-Setup.exe`  
**Verified SHA-256:** `07bf3f4ca13aae8e2644b7ddee5b054f4294161929a2a4562f91f5922603acf9`  
**Source basis:** private Salryn application SHA `ce554a2c354dce47952345862b3de59418a141de`, versioned Inno setup from packaging SHA `556e04efee98d78816ab63bce0bef653d07f0e68`.  
**Status:** source-derived prerequisite inventory. A clean Windows host installation was **not** performed as part of preparing this document.

## 1. Mandatory OS and access

| Dependency | Required for fresh host? | Evidence / action |
| --- | --- | --- |
| Windows x64, desktop session | **Yes** | Installer is Inno Setup x64-compatible; desktop project targets `net10.0-windows` with Windows Forms. Repo's published system guidance says Windows 10/11 64-bit. Prefer a supported Windows 11 release. |
| Administrator permission for setup/UAC | **Yes** | Inno `PrivilegesRequired=admin`; app runs afterward as the signed-in Windows operator. |
| Persistent local disk / write access | **Yes** | App files under `{autopf}\Kirjane Information Technology Services\Salryn POS`; SQLite and records under `%ProgramData%\Salryn`. Installer grants standard Users Modify to the Salryn data/backup/config/log/export/receipt directory trees. |
| Desktop display and keyboard/mouse | **Yes** | UI is a WinForms WebView2 desktop shell. Repo recommends at least 1366×768, 8 GB RAM, modern Core i3 and SSD (these are **recommendations**, not established hard minimums). |
| Available loopback TCP endpoint | **Yes** | Packaged API serves `http://127.0.0.1:5299`. Avoid binding another process to TCP 5299; no external internet server or inbound LAN exposure is required for standard local mode. |

## 2. Mandatory runtime dependencies

| Runtime | Required? | Supplied by QA1 setup? | Action |
| --- | --- | --- | --- |
| **.NET 10 Windows Desktop Runtime, x64** | **Yes** | **Not provisioned by Inno script** | Install current supported .NET 10 Windows Desktop Runtime x64, needed by `Salryn.Desktop.WebView.exe` (`net10.0-windows`, WinForms, framework-dependent publish). |
| **ASP.NET Core Runtime 10, x64** | **Yes** | **Not provisioned by Inno script** | Install current .NET 10 ASP.NET Core Runtime x64, needed by `Salryn.Api.Local.exe` (`Microsoft.NET.Sdk.Web`, `net10.0`, framework-dependent publish). |
| **Microsoft Edge WebView2 Evergreen Runtime** | **Yes** | **Evergreen bootstrapper included**; full runtime may be downloaded | Setup detects registry `pv` for the WebView2 client GUID. If absent, invokes bundled `MicrosoftEdgeWebview2Setup.exe /silent /install`. Internet is needed during initial setup when WebView2 is missing; reinstall/reboot if the runtime cannot be confirmed. |
| **WebView2Loader.dll and managed WebView2 package** | **Yes** | **Packaged in installed app** | Build script stages `WebView2Loader.dll` alongside shell and under `runtimes\win-x64\native`; no separate client download normally. |
| **React / Vite / JS front-end assets** | **Yes (as packaged files)** | **Packaged `dist\`** | Built frontend is served by local API; client does **not** install Node/npm or launch Vite. |
| **SQLite database engine** | **Yes (as app dependency)** | **Bundled by .NET project/publish** | Product uses file-based SQLite database; do **not** install SQL Server, MySQL or an external SQLite server. |

**Official download:** [Microsoft .NET 10 releases](https://dotnet.microsoft.com/en-us/download/dotnet/10.0). Install **both** applicable runtime families; `dotnet --list-runtimes` should show matching major versions of `Microsoft.NETCore.App`, `Microsoft.WindowsDesktop.App`, and `Microsoft.AspNetCore.App` at **10.x**. Installing only the Desktop Runtime does not establish the ASP.NET Core shared framework used by the local API.

**Runtime validation in PowerShell:**
```powershell
dotnet --list-runtimes
Get-NetTCPConnection -LocalPort 5299 -ErrorAction SilentlyContinue
```
An empty TCP result before launching Salryn is normal; after launch the local API should bind to loopback port 5299. The desktop startup is responsible for starting the packaged `Salryn.Api.Local.exe`.

## 3. License and configuration

| Item | Requirement | Supplied? |
| --- | --- | --- |
| Store/operator identity and authentication | Complete authorized first-run setup or sign in with valid store operator | User-specific / provisioned by deployment |
| Machine-bound Salryn license | Required to access entitled functionality | **Not provided as public credentials** in this package |
| Local configuration and install identity | Stored in `%ProgramData%\Salryn\config` and related data locations | Created/managed by application on legitimate setup |
| Internet for normal transactions | **Not continuously required** once app, license and runtimes are ready | May still be required for first-time WebView2 installation, activation/support/updates depending on provisioning |

Do not delete the `%ProgramData%\Salryn` tree or license material when upgrading an existing installation. The stable Inno `AppId` upgrades the app folder; installation does not erase operational data.

## 4. Printer, documents and peripherals — optional or workflow-specific

- **Windows printer driver** for the customer's exact USB/LAN/thermal receipt printer, if receipt printing is in scope.
- Supported **barcode scanner** (typically keyboard HID) and manufacturer drivers/config if needed.
- **Cash drawer interface** and printer-trigger connection when that hardware has been separately validated; not required just to run Salryn.
- A4 office printer or **Save as PDF** output depends on configured Windows capabilities and app document code. Current QA1's new PR/PO/RR/quotation/order/voucher modals use browser `window.print()`; this is **not** proof of a generated downloadable PDF file. Document print work is still a beta acceptance item.
- Optional UPS/power protection and external backup location for the store.

No payment gateway, BIR accreditation processing, cloud database or separate corporate server is installed merely by running this setup.

## 5. Fresh-host installation validation checklist

- [ ] Windows x64; working administrator account to approve Setup.
- [ ] `dotnet --list-runtimes` shows Windows Desktop 10.x + ASP.NET Core 10.x (and .NETCore.App 10.x).
- [ ] Obtain exact setup and verify full SHA-256 `07bf3f4ca13aae8e2644b7ddee5b054f4294161929a2a4562f91f5922603acf9`.
- [ ] WebView2 available, or internet working for bundled Evergreen bootstrapper.
- [ ] Other processes do not occupy local port 5299.
- [ ] Execute installer; find Start Menu/Desktop shortcut; launch app. No `dotnet run`, `npm run`, Docker or new Windows profile necessary.
- [ ] Confirm licensing/startup and legitimate account login without sharing secrets.
- [ ] Verify local app and API health; if failure, read sanitized logs in `%ProgramData%\Salryn\logs`.
- [ ] Confirm `%ProgramData%\Salryn\data\salryn.db` was initialized and local user can write WAL/SHM under its managed ACL.
- [ ] Verify screen rendering, basic non-committing catalog/stock paths, and close/reopen persistence.
- [ ] Configure real printer/scanner only if separately supplied, and capture actual output for acceptance.
- [ ] For client beta, record host, version, screenshot/QA result and unresolved issue list; do not treat the build as financial-production approved.

## 6. Build-machine dependencies — NOT required on end-user host

These apply only when building the installer: .NET SDK 10.x, Node.js/npm for React/Vite/TypeScript build, Inno Setup 6 `ISCC.exe`, packaging PowerShell, a Windows build environment, branded icon assets, and Microsoft's Evergreen bootstrapper for inclusion in the installer. This client branch is a **compiled distribution** and is not a source-development repository.

## 7. Evidence and limits

The installer source confirms `--self-contained false` for **both** .NET publishes; thus .NET runtimes are **external prerequisites** unless a new independently inspected installer version adds them. It includes the Microsoft **WebView2 bootstrapper**, not the full offline Evergreen Runtime. The native WebView2 loader and app assets are staged.

The distribution was independently checked for **byte size and SHA-256** against the supplied Google Drive installer. This document does **not** certify successful first-time host setup on every supported Windows build, signing/certificate status, supported client fiscal workflows or that the QA1 business audit is complete. Do not conflate those independent acceptance gates with dependency inventory.
