# Windows XC Hosting Options — Draft

!!! danger "Draft — Still Requires Final Review & Testing"
    Native Windows service hosting is not currently documented as a supported installation path. The options below require additional shutdown, upgrade, and database-safety testing.

## Current direction: Docker

The proposed Windows route is the [Docker installation](docker.md) using Docker Desktop's Linux-container backend. The XC container, persistent volume, and 60-second stop grace period remain the same as on other Docker hosts.

Host Caddy still needs to start reliably after a reboot and bind ports `80` and `443`. Caddy documents [Windows service installation](https://caddyserver.com/docs/running#windows-service) through either `sc.exe` or WinSW, but that part of the combined IPTVBoss setup has not completed final testing.

## Leading native option: WinSW

[WinSW](https://github.com/winsw/winsw) can wrap a console executable as a Windows service. A future native setup would likely use it to run the packaged `iptvboss-c.exe` with arguments equivalent to:

```text
-xcserver -directory C:\ProgramData\IPTVBoss-XC -xc-proxy -xc-bind-address loopback
```

Caddy would run as a separate Windows service and proxy the public hostname to `127.0.0.1:8001`.

Before this can be published as supported guidance, testing must confirm:

- Windows Service Control Manager stop and restart operations reach the JVM cleanly.
- The shutdown hook receives enough time to close the H2 databases.
- The service identity has the minimum required permissions for the application and data directories.
- Standard output, error output, wrapper logs, and upgrades have predictable locations and rotation.
- Reboot, crash recovery, backup, upgrade, and rollback scenarios preserve the database.

No downloadable WinSW configuration is provided while those questions remain open.

## Task Scheduler

Windows Task Scheduler can start `iptvboss-c.exe` at boot, but it offers weaker service supervision and less predictable shutdown handling than a tested service wrapper. It is therefore not the preferred always-on hosting model and is not included as an installation procedure.

## Not recommended

Do not register `iptvboss-c.exe` directly with `sc.exe`. A normal console launcher does not automatically implement the Windows service protocol expected by the Service Control Manager. A service-aware wrapper or an application-level Windows service integration is required.
