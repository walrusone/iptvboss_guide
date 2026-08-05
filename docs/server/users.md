# 💲 Server User Management

💲 [Pro feature — see Free vs Pro](../getting-started/free-vs-pro.md).

Server **User Management** controls users whose layouts, source credentials, and output links are managed through the XC Server.

## Open User Management

1. Open the **Server** menu.
2. Open **Dashboard**.
3. Unlock editing with the required administrator access.
4. Open the **User Management** section.

![Server users](../assets/images/server-console/server-users.png)

## Add or edit a user

1. Select **Add User** to create a user, or select an existing user.
2. Enter the **User Name** and optional **Email**.
3. Enable **User Enabled** when the user should receive output.
4. Assign one or more enabled layouts.
5. Add or update the user's source credentials.
6. Save the changes.
7. Wait for any backup or cloud-sync operation to finish.

Both the user and the assigned source credentials must be enabled. The assigned layout must also be enabled before output is expected.

## Output and access actions

- Use **Output M3Us** to generate or review user playlist output.
- Use **Reset XC Password** only when the user needs a new XC password.
- Use **Delete User** only after confirming that no player, layout, or customer still depends on the account.

!!! warning
    Password reset changes access. Deleting a user can remove access to assigned outputs. Confirm the intended user and preserve any required configuration before either action.

## Verify a server user

1. Confirm the user is enabled.
2. Confirm the assigned layout is enabled.
3. Confirm the source credentials are present and enabled.
4. Confirm the generated M3U link.
5. Confirm the XMLTV link when XC Server output is used.
6. Review server logs after saving changes.

Server changes affect the shared authoritative database. Do not edit the same users from another IPTVBoss or XC Server process at the same time.
