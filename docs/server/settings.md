# Server Settings

Use **Server** → **Settings** or the **Server Settings** section in the Server Console to configure the XC Server connection and scheduled work.

![Server Settings](../assets/images/server-console/server-settings.png)

## Configure the server connection

1. Open **Server Settings**.
2. Enter the server address in **Server URL**.
3. Enter the server listening port in **Server Port**.
4. Enable **Include Port in URL** when the public connection address requires the port to be included.
5. Review the displayed connection address.
6. Select **Save Settings**.

Use the address that is reachable by the paired desktop installations. Do not use `localhost` or `127.0.0.1` for a server that must be reached from another computer.

!!! warning
    The server URL in documentation and screenshots must be redacted. Never publish an administrator password, API key, pairing code, or private server address.

## Configure schedules

The **Current Schedules** section shows the configured server schedules and their actions.

1. Select **Add Schedule** to create a schedule.
2. Choose the actions that the schedule should run, such as source synchronization, EPG synchronization, M3U output, or XML output.
3. Choose the run times.
4. Enable the schedule when it is ready.
5. Select **Save Settings**.
6. Confirm that the schedule appears with its next run and status.

![Server schedule settings](../assets/images/server-console/server-settings.png)

Use **Edit Selected** or **Delete Selected** only after selecting the intended schedule. Use **Refresh** to reload the displayed schedule state.

## Review run history

The run history shows whether scheduled work succeeded or failed and when it last ran. Use the history to identify the first failed action, then open the server logs for details.

## Apply changes safely

1. Save one logical group of changes at a time.
2. Confirm the connection status after saving.
3. Run a controlled sync or output test.
4. Review the server logs.
5. Confirm the paired desktop installation can still connect.

If the server cannot be reached after a change, restore the previous connection values and review the logs before changing the port again.

!!! note "Version requirement"
    Server settings and schedule controls shown here target the IPTVBoss 3.11 server console.
