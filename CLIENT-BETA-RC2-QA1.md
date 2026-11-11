# Salryn POS — Windows client beta candidate `0.1.0-rc2.qa.1`

This **branch is a client-beta distribution/reference lane**, separate from `main`. It preserves `main`'s existing release files, descriptions and checksum.

**Current installer:** `Salryn-POS-v0.1.0-rc2.qa.1-Win-x64-QA-Setup.exe`  
**Original download:** [Google Drive — verified installer](https://drive.google.com/file/d/1WCUleoDV1AWKtNX-ch7xzABVjmViCB6O/view?usp=drive_link)  
**Size:** `24,370,315` bytes  
**SHA-256:** `07bf3f4ca13aae8e2644b7ddee5b054f4294161929a2a4562f91f5922603acf9`  
**Build (UTC):** `2026-11-11T07:30:26.3363903Z`  
**Application source SHA:** `ce554a2c354dce47952345862b3de59418a141de`  
**Packaging source SHA:** `556e04efee98d78816ab63bce0bef653d07f0e68`

**Read before installation:** [Fresh-host prerequisites and dependency checklist](docs/FRESH-HOST-DEPENDENCIES-RC2-QA1.md). The release folder contains the checksum and (if uploaded) repository-stored split parts plus a PowerShell assembler: [release directory](releases/0.1.0-rc2.qa.1/).

> **Version honesty:** `0.1.0-rc2.qa.1` is the actual package identity, an **internal QA candidate being used for controlled client-beta evaluation**. It is not a final approved production release. The feature set is undergoing C01–C11 Windows acceptance; printing and business-rule findings are still being worked through. Do not rename the package to RC2 final or imply a completed release gate.

This is a compiled Windows distribution. The private application source code, license material and client databases are **not** copied to this public repository.

## Fresh machine quick start

1. Use a **64-bit Windows 11 machine** (the public project's earlier minimum also lists Windows 10 x64, but Windows 10 is past standard Microsoft support). Have local administrator permission for setup.
2. Install **Microsoft .NET 10 Windows Desktop Runtime x64** and **Microsoft ASP.NET Core Runtime 10 x64** from [Microsoft's .NET 10 download page](https://dotnet.microsoft.com/en-us/download/dotnet/10.0). These are **runtime** installs, not the .NET SDK.
3. Ensure Microsoft Edge **WebView2 Evergreen Runtime** is present. The Salryn installer includes Microsoft's Evergreen **bootstrapper**, which will attempt installation when necessary; internet access is required if it must download the runtime.
4. Download the installer from the verified Drive link above, or reassemble the repository parts if available. Before execution, run `(Get-FileHash -Algorithm SHA256 .\Salryn-POS-v0.1.0-rc2.qa.1-Win-x64-QA-Setup.exe).Hash` and compare with the full expected checksum.
5. Run setup with Windows UAC approval; launch Salryn POS from Start Menu/Desktop. The desktop shell starts the packaged local API host automatically. Do not manually launch Node, Docker, SQLite or a terminal.
6. Complete legitimate store initialization/operator login and machine-bound licensing with the authorized Kirjane support channel. The installer does **not** contain customer activation credentials or an already activated client license.
7. Verify local app launch, login, catalog, stock, UI and permitted non-financial flows. For controlled beta tests, record the version/checksum and capture screenshots. Avoid live payments/returns/settlements until the relevant acceptance gates are signed.

**Local API:** `http://127.0.0.1:5299`; normal deployment is localhost-only.  
**Database:** `%ProgramData%\Salryn\data\salryn.db`; backup/license/config/logs live beneath `%ProgramData%\Salryn`.  
**Default application directory:** under `%ProgramFiles%\Kirjane Information Technology Services\Salryn POS` (the installer may reuse a prior directory during upgrades).

## What is NOT required for clients

No .NET SDK, Node.js, npm, Vite, React development server, Docker, WSL, Inno Setup compiler, Visual Studio, standalone SQLite server or PostgreSQL/MySQL service. Those are build/development concerns, or databases the product does not use in its local installation.

## Additional client equipment

Printer, barcode scanner, cash drawer, terminal hardware and appropriate manufacturer drivers are **optional and workflow-dependent**, not supplied by the installer. POS receipt printing is not equivalent to formally generated PDF business-document export. Official tax/fiscal accreditation and payment-provider integrations are **not implied** by this beta package.

For evidence or support, provide the installed version, installer checksum, Windows version and sanitized application logs; do **not** publish the store database, license key, passwords or machine-bound secrets.
