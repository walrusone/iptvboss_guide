# Installing IPTVBoss

This page explains how to install the current IPTVBoss release on Windows, macOS, or Linux.

## Before you begin

- Use a computer or server that you control.
- Confirm whether your computer uses an Intel/AMD or ARM processor.
- Have your IPTVBoss account or license information available if you use [Pro features](../settings/pro.md).
- Choose a location where IPTVBoss can keep its local database and output files.

!!! warning
    Do not download IPTVBoss from an unofficial mirror. Use the official IPTVBoss download page and verify the release and operating-system package before installing.

## Download the installer

Start with the [official IPTVBoss download page](https://walrusone.github.io/iptvboss-release/download.html) and select the current release for your operating system.

The [GitHub Releases page](https://github.com/walrusone/iptvboss-release/releases/latest) is also available when you need to inspect release notes or download assets directly.

Choose the asset for your operating system and processor:

| Operating system | Processor | Asset to choose |
| --- | --- | --- |
| Windows | Intel or AMD | Windows x64 installer |
| macOS | Intel | macOS x64 installer |
| macOS | Apple Silicon | macOS ARM64 installer |
| Linux | Intel or AMD | Linux x64 package |
| Linux | ARM64 | Linux ARM64 package |

The exact asset names may change between releases. Do not substitute a beta or alpha download unless a [support ticket](https://members.bosstees.net/) specifically requires it.

## Windows

1. Download the Windows x64 installer.
2. Run the downloaded installer.
3. Follow the installation prompts.
4. Start **IPTVBoss** from the Start menu or installed shortcut.

If Windows displays a security prompt, confirm that the file came from the official release page before choosing whether to continue.

## macOS

1. Download the installer matching your Mac’s processor.
2. Open the downloaded installer.
3. Move or install **IPTVBoss** as prompted.
4. Start **IPTVBoss** from Applications.

On the first launch, macOS may ask you to confirm that you want to open an application downloaded from the internet. Only approve the prompt for a download you obtained from the official release page.

## Linux

1. Download the package matching your Linux architecture.
2. Install the package using your distribution’s package manager.
3. Start **IPTVBoss** from the applications menu or its installed command.

For Debian-based Linux, the release package uses the system architecture in its filename. The download page provides the current package version; replace `3.11.16` below with the version shown there:

```bash
CPU=$(dpkg --print-architecture)
wget https://github.com/walrusone/iptvboss-release/releases/latest/download/iptvboss_3.11.16_${CPU}.deb
sudo apt install ./iptvboss_3.11.16_${CPU}.deb
```

The package format and installation command may vary by release. Follow the instructions attached to the selected release rather than copying a command from an older version.

## Confirm the installation

1. Start IPTVBoss.
2. Wait for the main application window to open.
3. Confirm that the menu bar includes **Sources**, **Layout**, **Output**, **Settings**, and **Logs**.
4. Continue to [Completing the first run](first-run.md).

!!! note
    Installation screens and package names may change between releases. Follow the current release notes when they differ from this page.
