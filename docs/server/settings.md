# Server Dashboard and GUI Settings

Use the XC Server Dashboard and the desktop **Server Settings** dialog to connect IPTVBoss installations to the server. The normal setup is a short pairing flow: generate a temporary code on the server, enter it in the desktop GUI, and link the installation.

## The setup flow

1. Start or open the XC Server.
2. On the server host, open **Server** → **Dashboard**.
3. Confirm that the dashboard reports a healthy connection and that the server is ready.
4. Select **Generate Pairing Code**.
5. On the desktop installation to be paired, open **Settings** → **Server Settings**.
6. Enter the reachable **Server URL** and **Server Port**.
7. Enable **Include Port in URL** when the public connection address requires it.
8. Enter a recognizable **Client Name**.
9. Enter the temporary **Pairing Code**.
10. Select **Link XC Server**.
11. Confirm that the desktop installation reports that it is paired.
12. Return to the dashboard and confirm that the client appears in the paired-client list.

![XC Server Dashboard](../assets/images/server/server-dashboard.png)

![Desktop XC API Settings](../assets/images/server/server-settings-1.png)

![Desktop XC API Settings: output and security](../assets/images/server/server-settings-2.png)

!!! warning
    The pairing code and server address can grant access to the installation. Do not publish them in screenshots or support requests. Pairing codes are temporary credentials.

## Configure the desktop GUI

Open **Settings** → **Server Settings** in the desktop application. The GUI contains sections for cloud database, server and network, output and links, security, and operations.

### XC Cloud Database

1. Enter the **Server URL**.
2. Enter the **Server Port**.
3. Enable **Include Port in URL** when required by the public address.
4. Review the displayed connection address.
5. Link the installation before enabling cloud database synchronization.

After pairing, the dialog can show cloud status and provide actions such as **Refresh Cloud Status**, **Disable Sync**, **Retry Current Database**, and **Unlink This Installation**. These actions apply immediately.

### Server and Network

Use **Enable XC Server** when the installation should provide the XC Server service. Use **Block direct connections (bind to 127.0.0.1)** only when the server must be local-only; that setting prevents remote paired devices from reaching it.

### Output and Links

The output section controls whether the server exposes M3U downloads, cloud-provider links, and TinyURL links. Enable only the link types required by the deployment.

### Security

The security section controls the administrator password and which areas require administrator protection, including logs, metadata, server settings, and users.

### Operations and Sync Runs

Use the update interval, testing-server controls, and **Manage Sync Runs** only when you understand their effect on the shared server. Sync runs are managed from the schedule editor.

## Configure server settings and schedules

Use the **Server Settings** section in the Server Console for server-side connection and schedule controls.

1. Enter the server address in **Server URL**.
2. Enter the server listening port in **Server Port**.
3. Enable **Include Port in URL** when the public connection address requires the port to be included.
4. Review the displayed connection address.
5. Select **Save Settings**.

Use the address that is reachable by the paired desktop installations. Do not use `localhost` or `127.0.0.1` for a server that must be reached from another computer.

!!! warning
    Never publish an administrator password, API key, pairing code, or private server address.

The **Current Schedules** section shows configured server schedules and their actions.

1. Select **Add Schedule** to create a schedule.
2. Choose source synchronization, EPG synchronization, M3U output, or XML output.
3. Choose the run times.
4. Enable the schedule when it is ready.
5. Select **Save Settings**.
6. Confirm that the schedule appears with its next run and status.

![Server console settings](../assets/images/server-console/server-settings.png)

Use **Edit Selected** or **Delete Selected** only after selecting the intended schedule. Use **Refresh** to reload the displayed schedule state.

## Apply changes safely

1. Save one logical group of changes at a time.
2. Confirm the connection status after saving.
3. Run a controlled sync or output test.
4. Review server logs and run history.
5. Confirm paired desktop installations can still connect.

!!! note
    Server dashboard, GUI settings, and schedule labels may change between releases.
