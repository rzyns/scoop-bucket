# Rzyns Scoop Bucket

[![Tests](https://github.com/rzyns/scoop-bucket/actions/workflows/ci.yml/badge.svg)](https://github.com/rzyns/scoop-bucket/actions/workflows/ci.yml) [![Excavator](https://github.com/rzyns/scoop-bucket/actions/workflows/excavator.yml/badge.svg)](https://github.com/rzyns/scoop-bucket/actions/workflows/excavator.yml)

A personal [Scoop](https://scoop.sh) bucket for Windows apps, including projects I build and tools I use.

## Install

```powershell
scoop bucket add rzyns https://github.com/rzyns/scoop-bucket
scoop install rzyns/diffusion-toolkit
scoop install rzyns/imgbrd-grabber
scoop install rzyns/skillranker
```

| Package | Description |
| --- | --- |
| `diffusion-toolkit` | Metadata viewer and organizer for AI-generated images. Requires the [.NET 10 Desktop Runtime (x64)](https://dotnet.microsoft.com/en-us/download/dotnet/10.0). |
| `imgbrd-grabber` | Customizable imageboard downloader, with GUI and command-line tools. |
| `skillranker` | `sr`, which recommends the most useful agent skills for the next step of a coding-agent session. Ranking requires a [TypeSafe.ai](https://console.typesafe.ai) API key (`TYPESAFE_API_KEY`). |

Update installed apps with `scoop update *`.

## Add an app

1. Publish a versioned Windows release, preferably a portable ZIP, with a stable download URL. Put each supported architecture in its own archive.
2. Copy `bucket/app-name.json.template` to `bucket/<app-name>.json`. Use a lowercase, hyphenated package name.
3. Fill in the version (without the leading `v`), description, homepage, license, download URL, and SHA256 checksum. Compute the checksum from the actual release archive:

   ```powershell
   Get-FileHash .\my-app_1.0.0_windows_x64.zip -Algorithm SHA256
   ```

4. Set `bin` to the executable path inside the ZIP. For a desktop app, replace `bin` with `shortcuts`, following the existing manifests. Add `extract_dir` if the archive wraps its files in a directory. Remove unsupported architectures and document any runtime requirements.
5. Set `checkver.github` to the app's GitHub repository and make the `autoupdate` URL match its release naming scheme. `$version` expands to the version without `v`. Add `persist` only for settings/data the app actually stores inside its installation folder.
6. Run the checks below, then try installing the manifest. Verify that it launches and that an update preserves user data before submitting it.

   ```powershell
   .\bin\checkver.ps1 <app-name>
   .\bin\checkurls.ps1 <app-name>
   .\bin\checkhashes.ps1 <app-name>
   .\bin\test.ps1
   scoop install .\bucket\<app-name>.json
   ```

The helper scripts use your installed Scoop. Tests require PowerShell 5.1 or newer, [Pester 5.2+](https://pester.dev), and BuildHelpers 2.0.1+. Install the test modules once if needed:

```powershell
Install-Module Pester -MinimumVersion 5.2.0 -Scope CurrentUser -Force
Install-Module BuildHelpers -MinimumVersion 2.0.1 -Scope CurrentUser -Force
```

See Scoop's [manifest documentation](https://github.com/ScoopInstaller/Scoop/wiki/App-Manifests) and [autoupdate documentation](https://github.com/ScoopInstaller/Scoop/wiki/App-Manifest-Autoupdate) for installers, dependencies, persistence, and other release formats.

## Maintenance

- CI checks manifests and repository formatting in Windows PowerShell and PowerShell 7.
- Excavator checks for app updates every four hours and commits updated URLs and checksums to the default branch. Both `checkver` and `autoupdate` are needed for this to work.
- GitHub may disable scheduled workflows after 60 days without repository activity. If updates stop, open [Excavator](https://github.com/rzyns/scoop-bucket/actions/workflows/excavator.yml), enable it if needed, and select **Run workflow**.
- Dependabot proposes weekly updates to the pinned GitHub Actions dependencies.

Release archives and extracted app files belong outside this repository; commit manifests and maintenance files only.
