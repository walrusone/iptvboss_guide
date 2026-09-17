# Installing IPTVBoss

This page explains how to install the current IPTVBoss release on Windows, macOS, or Linux.

For an always-on XC Server, start with [Choose an Installation](../server/setup/index.md).

## Before you begin

- Use a computer or server that you control.
- Confirm whether your computer uses an Intel/AMD or ARM processor.
- Have your IPTVBoss account or license information available if you use <span class="pro-badge">PRO</span> [Pro features](free-vs-pro.md).
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

For Ubuntu and other Debian-based distributions:

1. Open the [official download page](https://walrusone.github.io/iptvboss-release/download.html) and select **Linux**.
2. Copy the command block under **Or install it from the command line**.
3. Paste it into a terminal on the computer where you want to install IPTVBoss and run it. Enter your account password and approve installation if prompted.
4. Start **IPTVBoss** from the applications menu.

The command detects your CPU architecture, downloads the matching `.deb`, and installs it with `apt`. Always copy the command from the download page so it uses the current release filename. If `wget` is missing, install it with `sudo apt install wget`, then run the download command again.

For a headless server, follow [Linux XC Server Installation](../server/setup/linux-service.md) instead of opening the desktop application. For other Linux distributions, use the package and instructions provided for your platform on the download page.

## Confirm the installation

1. Start IPTVBoss.
2. Wait for the main application window to open.
3. Confirm that the menu bar includes **Sources**, **Layout**, **Output**, **Settings**, and **Logs**.
4. Continue to [Completing the first run](first-run.md).

!!! note
    Installation screens and package names may change between releases. Follow the current release notes when they differ from this page.
