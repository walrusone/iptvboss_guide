# Desktop GUI Settings

💲 [Pro feature — see Free vs Pro](../getting-started/free-vs-pro.md).

Open **Settings** → **Server Settings** in the desktop IPTVBoss application. These settings connect the desktop installation to the XC Server and control its server, network, output, security, and synchronization behavior.

![Desktop XC API Settings](../assets/images/server/server-settings-1.png)

![Desktop XC API Settings: output and security](../assets/images/server/server-settings-2.png)

## XC Cloud Database

1. Enter the **Server URL** and **Server Port**.
2. Enable **Include Port in URL** when required by the public address.
3. Review the displayed connection address.
4. Link the installation before enabling cloud database synchronization.

After pairing, actions such as **Refresh Cloud Status**, **Disable Sync**, **Retry Current Database**, and **Unlink This Installation** apply immediately.

## Bootstrap an empty XC Server with GUI pairing

An IPTVBoss Pro desktop installation can initialize a new XC Server using the database currently open in the GUI:

1. Open `https://boss.domain.com/boss.php` on the new server.
2. Copy the server URL and one-time pairing code shown under **Link from IPTVBoss**.
3. In the desktop application, open **Settings** → **Server Settings**.
4. Enter the server URL and pairing code, then select **Link XC Server**.
5. Confirm that the current GUI database should become the authoritative server database.
6. Wait for the upload and server bootstrap to finish, then verify the connection and backup status.

The pairing code expires and can be used only once. Confirm that the intended database is open and backed up before linking because it becomes the initial authoritative database for the server. A non-Pro installation can pair only after bootstrap has been completed.

## Server and Network

Use **Enable XC Server** when this installation should provide the XC Server service. Use **Block direct connections (bind to 127.0.0.1)** only when the server must be local-only; it prevents remote paired devices from reaching it.

Enabling the XC Server does not require any layout to be enabled for XC output. The server can be used solely to deliver M3U playlists. Enable **XC Enabled** on a layout only when that particular layout should participate in XC Server output.

## Output, links, security, and operations

- **Output and Links** controls M3U downloads, cloud-provider links, and TinyURL links. Enable only the link types required by the deployment.
- **Security** controls the administrator password and which console areas require administrator protection.
- **Operations and Sync Runs** contains the update interval, testing-server controls, and **Manage Sync Runs**. Use these only when you understand their effect on the shared server.

!!! warning
    Never publish an administrator password, API key, pairing code, or private server address.
