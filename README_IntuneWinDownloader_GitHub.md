# IntuneWin Downloader

IntuneWin Downloader is a Windows troubleshooting tool for Intune Win32 app packages.

It lists available Win32 apps from the Company Portal catalog, downloads the selected package through the local Intune client flow, decrypts and extracts the content, exports metadata, and can run the extracted install command locally for troubleshooting.

The goal is simple: recover and inspect the package that the device or user is already allowed to receive, then test the installer locally without digging through multiple logs and folders.

## Important disclaimer

This project is not a Patch My PC product.

It is not supported, maintained, endorsed, or serviced by Patch My PC. Do not open Patch My PC support cases for this tool. Patch My PC does not provide warranty, service level agreements, troubleshooting assistance, release guarantees, or production support for this project.

This project is also not a Microsoft product and is not supported by Microsoft.

Use it as a community troubleshooting tool only.

## What the tool does

IntuneWin Downloader can:

1. Load the available Win32 apps shown through the Company Portal app catalog.
2. Show app icons, name, version, publisher, and availability status.
3. Select one or multiple apps.
4. Download multiple selected apps in parallel.
5. Decrypt and extract the downloaded IntuneWin content.
6. Export useful metadata next to the extracted package.
7. Parse Package.xml metadata when available.
8. Build a local install command from the extracted package metadata.
9. Run a local install test from the extracted content folder.
10. Optionally remove common silent switches so installer UI can be shown.
11. Run silent install tests as SYSTEM through a temporary scheduled task.
12. Show which downloaded apps are ready for install testing.

## What the tool does not do

The tool does not bypass Intune assignments.

It can only download content that the current device or current user can already access through the normal Intune client side flow.

The tool does not grant access to apps that are not assigned. It does not modify Intune assignments. It does not change tenant configuration. It does not replace the Intune Management Extension.

The local install test is only a troubleshooting helper. It does not fully reproduce the complete Intune Management Extension flow. Detection rules, requirement rules, dependencies, supersedence, retry handling, restart handling, reporting, ESP behavior, and assignment logic are not fully emulated.

## Intended use

This tool is intended for lab work and troubleshooting scenarios such as:

1. The original Win32 app source files are no longer available.
2. You need to inspect the extracted package content.
3. You want to verify what installer command is available from the package metadata.
4. You want to test the installer locally on a device.
5. You want to collect package metadata for troubleshooting.
6. You want to compare downloaded package content with expected content.

Use it on test devices first.

## Requirements

The device must be a Windows device that is already managed by Intune.

Recommended requirements:

1. Windows 10 or Windows 11.
2. Microsoft Intune Management Extension installed.
3. Company Portal available for the signed in user.
4. Local admin rights for download, extraction, and install testing.
5. Windows App Runtime installed when using the framework dependent WinUI build.
6. .NET 8 Desktop Runtime installed when using the framework dependent build.

Some functionality depends on local Intune state, local certificates, and user token availability. If the device is not properly enrolled or the user context is broken, loading or downloading apps may fail.

## How it works

The app list comes from the Company Portal catalog.

The package download uses the local Intune client side content flow. The tool resolves the required Intune context, requests package content information, downloads the encrypted content, decrypts it locally, and extracts the package to the selected output folder.

The extracted folder and metadata are written under the selected download folder.

## Output folder

By default, packages are saved under:

```text
C:\Temp\IntuneWinDownloader
```

Each app gets its own folder. The output usually contains:

```text
Extracted
Metadata
Downloaded package files
Decoded package files
Logs
```

The metadata folder can contain package details, content information, and parsed Package.xml data. Sensitive values such as content URLs, encryption keys, tokens, and similar values should not be published.

## Install testing

After an app is downloaded and extracted, the Install test column shows that the app is ready.

The install test window shows the command that will be executed. You can edit the command before running it.

Silent install testing can run in the current elevated process or as SYSTEM. Visible installer UI cannot run as SYSTEM because SYSTEM runs in session 0. When silent switches are removed, the tool forces the install test back to the current elevated user context.

The install test captures:

```text
Exit code
stdout
stderr
MSI log path when available
Wrapper command path
```

An exit code of 0 only means that the installer process returned success. It does not always mean the app is correctly installed. Always verify the result.

## Multi app download

You can select multiple apps by checking the selection box next to each app.

The Download selected button downloads all selected apps. Downloads run in parallel with a limited worker count to avoid overloading the local client flow.

Downloaded apps show as ready for install testing.

## Security notes

This tool handles local Intune related state and package download metadata.

Do not publish exported metadata without reviewing it first. Metadata can contain environment details and may contain values that should stay private.

Do not run install tests on production devices unless you understand what the installer does.

Do not remove silent switches unless you expect and allow installer UI to appear.

Do not use this tool to redistribute software packages unless you have the rights to do so.

## Privacy notes

The tool runs locally on the device.

It reads local Intune client state and uses local user or device context needed to request content that is already available to that device or user.

The tool does not intentionally upload package content or metadata to an external service.

## Known limitations

1. Store style apps are not the focus of this tool.
2. Detection rules are not fully emulated.
3. Requirement rules are not fully emulated.
4. Dependencies and supersedence are not fully emulated.
5. Intune reporting is not updated by local install tests.
6. Local install tests do not create a real Intune install record.
7. Some token or catalog failures can happen when the local user state is broken.
8. Some packages contain vendor specific wrapper logic that may not behave exactly like IME.
9. Some metadata fields depend on what the Company Portal catalog and package expose.

## Recommended troubleshooting flow

1. Start the app as administrator.
2. Let the startup process load the app list.
3. Select one or more apps.
4. Download the selected apps.
5. Open the extracted folder.
6. Review the metadata folder.
7. Select a downloaded app.
8. Click Install test.
9. Review the command before running it.
10. Run the install test.
11. Review the exit code and logs.

## Building from source

Open the solution in Visual Studio 2022.

Recommended workloads:

```text
.NET desktop development
Desktop development with C++
Windows App SDK components
```

Build as x64.

If you build a framework dependent version, make sure target devices have the required Windows App Runtime and .NET Desktop Runtime installed.

If you build a self contained version, expect a larger output folder and additional build requirements.

## Distribution

Do not distribute only the exe.

Distribute the full publish output folder, including all required DLLs, assets, and runtime files that are produced by the publish process.

For Intune deployment, wrap the full publish folder as a Win32 app and install it to a fixed location such as:

```text
C:\Program Files\IntuneWinDownloader
```

Create a Start Menu shortcut that launches the app elevated.

## Support

There is no official support.

This project is provided as is.

Issues, pull requests, and community feedback are welcome through GitHub, but there is no guarantee of response time, fixes, or continued development.

Do not contact Patch My PC support for this tool.

## License

Add your license here before publishing the repository.

Recommended options for a public GitHub project are MIT, Apache 2.0, or another license that matches how you want others to use, modify, and redistribute the code.

Until a license is added, the default copyright rules apply and others do not automatically have permission to reuse or redistribute the code.

## Credits

This tool was built to make Intune Win32 app troubleshooting easier by combining app catalog visibility, package download, extraction, metadata export, and local install testing into one interface.

Product names, icons, and publisher names shown in the app list come from the local app catalog data exposed to the device or user.
