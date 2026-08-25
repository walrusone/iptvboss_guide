# Install the XC Server on Windows

--8<-- "includes/xc-server-preview.md"

!!! danger "Still requires Windows testing"
    This Task Scheduler procedure is not a fully tested Windows service installation. Task Scheduler can start IPTVBoss automatically, but it does not provide the same graceful shutdown guarantees as a true service wrapper. Reboot, shutdown, upgrade, and database-recovery testing is still required.

This setup uses two Windows components:

- **Task Scheduler** starts `iptvboss-c.exe` in the background when Windows starts.
- **Caddy** runs as a Windows service and provides the public HTTPS address.

IPTVBoss listens only on `127.0.0.1:8001`. Caddy is the only service exposed to the network.

## 1. Prepare the hostname and network

Choose a hostname such as `boss.example.com`. Before continuing:

1. Create a DNS `A` record that points the hostname to your public IPv4 address. Add an `AAAA` record only when the computer has working public IPv6.
2. Forward TCP ports `80` and `443` from the router to the Windows computer.
3. Confirm that another web server is not already using ports `80` or `443`.
4. Configure Windows so the computer does not sleep while it is serving clients.

## 2. Install IPTVBoss

Install the Windows x64 release from the [official IPTVBoss download page](https://walrusone.github.io/iptvboss-release/download.html). Open IPTVBoss once after installation, approve Windows security prompts only when the installer came from the official site, and then close the desktop application.

Open PowerShell and locate the console launcher:

```powershell
(Get-Command iptvboss-c.exe).Source
```

Copy the complete path returned by PowerShell. If the command is not found, locate `iptvboss-c.exe` in the IPTVBoss installation directory and copy its full path. The scheduled task must use the console launcher, not the normal desktop shortcut.

Create a dedicated data directory:

```powershell
New-Item -ItemType Directory -Force "$env:LOCALAPPDATA\IPTVBoss-XC"
```

The task must run under this same Windows account so it can access the data directory and the installed application.

## 3. Create the startup task

Open **Task Scheduler** from the Start menu. Select **Task Scheduler Library**, then choose **Create Task**. Do not use **Create Basic Task**, because it does not show all required settings.

### General tab

1. Set **Name** to `IPTVBoss XC Server`.
2. Set the user account to the same normal Windows account used above. Do not use `SYSTEM`.
3. Select **Run whether user is logged on or not**.
4. Leave **Run with highest privileges** cleared; IPTVBoss does not need administrator access.
5. Do not select **Do not store password**. The server requires normal network access.

Windows asks for this account's password when the task is saved. Enter the account password, not its Windows Hello PIN.

### Triggers tab

1. Select **New**.
2. Set **Begin the task** to **At startup**.
3. Enable **Delay task for** and select `30 seconds`.
4. Make sure **Enabled** is selected, then choose **OK**.

The short delay gives Windows networking time to initialize. Caddy may briefly return a gateway error until IPTVBoss becomes healthy.

### Actions tab

Select **New**, then choose **Start a program**.

For **Program/script**, enter the full path to `iptvboss-c.exe` found earlier. Do not put quotation marks around the value in this field.

For **Add arguments**, enter one line, replacing `YOUR_USERNAME` with the Windows account's folder name:

```text
-xcserver -directory "C:\Users\YOUR_USERNAME\AppData\Local\IPTVBoss-XC" -xc-proxy -xc-bind-address loopback
```

For **Start in**, enter the directory containing `iptvboss-c.exe`, without quotation marks. Choose **OK**.

### Conditions tab

For a computer that should serve continuously:

- Clear **Start the task only if the computer is on AC power**.
- Clear **Stop if the computer switches to battery power**.
- Do not require the computer to be idle.

Keep the power restrictions instead when preserving laptop battery life is more important than server availability.

### Settings tab

Configure these values:

- Enable **Allow task to be run on demand**.
- Enable **If the task fails, restart every** `1 minute`, with `3` restart attempts.
- Clear **Stop the task if it runs longer than**. The XC Server is intended to run continuously.
- Set **If the task is already running** to **Do not start a new instance**.
- Clear **If the running task does not end when requested, force it to stop** when that option is available.

Choose **OK**, enter the Windows account password, and save the task.

## 4. Test IPTVBoss

Select **IPTVBoss XC Server** in Task Scheduler and choose **Run**. After a short delay, check:

```powershell
curl.exe --fail http://127.0.0.1:8001/healthz
```

The task should remain in the **Running** state. If it exits, inspect **Last Run Result**, enable Task Scheduler history, and review the logs in:

```text
C:\Users\YOUR_USERNAME\AppData\Local\IPTVBoss-XC
```

Do not start the normal IPTVBoss desktop application against this same data directory while the scheduled XC Server is running.

## 5. Install Caddy for Windows

Download the Windows amd64 archive from Caddy's [official download page](https://caddyserver.com/download) and extract `caddy.exe` into:

```text
C:\Caddy
```

Open Notepad as Administrator and create `C:\Caddy\Caddyfile`. When saving, choose **All files** so Windows does not add a `.txt` extension.

Enter this configuration, replacing the example hostname:

```caddyfile
boss.example.com {
    reverse_proxy 127.0.0.1:8001
}
```

Open **Command Prompt as Administrator** and validate it:

```bat
C:\Caddy\caddy.exe validate --config C:\Caddy\Caddyfile --adapter caddyfile
```

Create and start the automatic Caddy service:

```bat
sc.exe create caddy start= auto binPath= "C:\Caddy\caddy.exe run --config C:\Caddy\Caddyfile --adapter caddyfile"
sc.exe start caddy
sc.exe query caddy
```

The spaces after `start=` and `binPath=` are required by `sc.exe`.

Allow inbound TCP ports `80` and `443` in **Windows Defender Firewall with Advanced Security**. Create inbound port rules for those two TCP ports only. Do not open IPTVBoss port `8001` in the firewall or forward it from the router.

`sc.exe` is appropriate for Caddy because Caddy officially supports this Windows service arrangement. Do not register `iptvboss-c.exe` with `sc.exe`; the IPTVBoss console launcher does not implement the Windows service protocol.

## 6. Open the Server Console

Allow a short time for Caddy to obtain the first certificate, then test the public address:

```powershell
curl.exe --fail https://boss.example.com/healthz
```

Open the Server Console:

```text
https://boss.example.com/boss.php
```

On a new installation, the first visit creates the administrator account. Continue with [first-time setup](index.md#after-installation).

## Common operations

Use Task Scheduler to check the IPTVBoss task state or start it on demand. Use an Administrator Command Prompt for Caddy:

```bat
sc.exe query caddy
sc.exe stop caddy
sc.exe start caddy
```

After editing the Caddyfile, validate and reload it without stopping Caddy:

```bat
C:\Caddy\caddy.exe validate --config C:\Caddy\Caddyfile --adapter caddyfile
C:\Caddy\caddy.exe reload --config C:\Caddy\Caddyfile --adapter caddyfile
```

## Back up and upgrade carefully

Task Scheduler's **End** command is not guaranteed to give the JVM enough time to run its normal database shutdown hook. Treat ending the background task as potentially abrupt.

Before an upgrade:

1. Wait until all Server Console synchronization and backup activity has finished.
2. Open [**Database Backup History**](../dashboard.md) in the Server Console and download a current backup.
3. End the scheduled task only after the downloadable backup is complete.
4. Confirm in Task Manager that `iptvboss-c.exe` has exited.
5. Copy the entire `IPTVBoss-XC` data directory to another computer or storage device.
6. Install the update, confirm the console launcher path is still correct, and run the task again.

Do not overwrite or restore live database files while the scheduled task is running. The shutdown and upgrade limitations are why this procedure remains marked as untested.

## Troubleshooting

| Problem | What to check |
| --- | --- |
| The scheduled task exits immediately | Verify the full executable path, **Start in** directory, username in `-directory`, task credentials, and **Last Run Result**. |
| Windows will not save or run the background task | Confirm the selected account has a password and enter that password instead of a Windows Hello PIN. |
| The task stops after three days | Edit the task and clear **Stop the task if it runs longer than**. |
| The task runs twice | Set the multiple-instance option to **Do not start a new instance** and confirm no second startup task exists. |
| The local health check fails | Confirm the task is running and inspect the IPTVBoss data-directory logs. |
| Caddy service returns error 1067 | Validate the Caddyfile. To see the error directly, stop the service and run the same `caddy.exe run` command in an Administrator Command Prompt. Press <kbd>Ctrl</kbd>+<kbd>C</kbd> afterward, then start the service again. |
| Caddy cannot obtain a certificate | Confirm DNS, router forwarding, Windows Firewall, and that no other program owns TCP ports `80` or `443`. |
| The public URL reports a gateway error | Confirm `curl.exe http://127.0.0.1:8001/healthz` succeeds before troubleshooting Caddy. |

For a more strongly supervised deployment today, use the [Docker installation](docker.md). If a reverse proxy cannot be used, see [Direct HTTPS](direct-https.md).
