# Adding a Playlist

IPTVBoss can import an M3U playlist from a URL or local file. It can also import a provider connection through the API source workflow.

!!! note "Required before saving"
    A new playlist source must go through **Manage Categories** before it can be saved. Use **Refresh Categories** inside that view to load the provider's current categories.

!!! warning
    Use only playlist sources that you are authorized to access. Treat provider URLs, usernames, and passwords as private credentials.

## Manage Categories

Use **Manage Categories** while adding or editing a playlist source to load the provider's current categories and choose which categories the source should include.

The dialog shows separate lists for **Live Categories**, **VOD Categories**, and **Series Categories**. The count below each list reports how many categories are shown, how many exist in total, and how many are stale. Use the filter field above a list to narrow the visible categories.

1. Select **Manage Categories** in the source dialog.
2. Select **Refresh Categories** in the category view.
3. Wait for the provider's current categories to load.
4. Click a category to select it. Use Ctrl-click on Windows/Linux or Command-click on macOS to select multiple categories.
5. Right-click the selected categories to use the available actions:
   - **Select All** selects every category in that list.
   - **Enable Selected** includes the selected categories in the source.
   - **Disable Selected** excludes the selected categories from the source.
   - **Clear Selected Stale** removes selected categories that are no longer current, when stale categories are present.
6. Repeat the selection and enable/disable actions independently in the Live, VOD, and Series lists.
7. Close the category view to keep the selection, then save the source dialog.

![Manage playlist categories](../assets/images/epg/manage-refresh-categories.png)

## Add an M3U source

1. Open **Sources**.
2. Select **Add M3U Source**.
3. Enter a descriptive value in **Name**.
4. Enter the playlist address in **Source Link**, or select **Browse** to choose a local `.m3u` file.
5. If the source requires authentication, enable the username/password option and enter the provider credentials.
6. Select [**Manage Categories**](#manage-categories) before saving the source.
7. In the category view, select **Refresh Categories** and wait for IPTVBoss to load the provider's current categories.
8. Select the categories you want to import, then close the category view.
9. Review the output and category options.
10. Select **Save**.

![The Add M3U Source dialog](../assets/images/sources/add-m3u-source.png)

## Add an API source

Use the API workflow when your provider uses the standard Xtream Codes API format. This is the recommended choice for a link such as:

`http://provider.example:8080/get.php?username=myuser&password=mypass&type=m3u_plus&output=ts`

Do not paste the entire `get.php` link into **Source Link** in the API dialog. Extract the three API fields from it:

- **Address**: `http://provider.example:8080/` — keep the scheme, hostname, and port; remove `/get.php` and everything after the `?`.
- **Username**: the value after `username=` — `myuser` in the example.
- **Password**: the value after `password=` — `mypass` in the example.

If a value contains URL-encoded characters such as `%2B` or `%40`, copy the value exactly as provided rather than changing it. If the provider gives you a standard XC link with a different filename or additional query parameters, the same rule applies: use the server portion as the address and copy the `username` and `password` parameter values.

1. Open **Sources**.
2. Select **Add API Source**.
3. Enter the provider server address.
4. Enter the supplied username and password.
5. Select [**Manage Categories**](#manage-categories) before saving the source.
6. In the category view, select **Refresh Categories** and wait for the category list to load.
7. Select the categories and content types to include.
8. Review whether VOD or series content should be included in the M3U output.
9. Select **Save**.

![The Add API Source dialog](../assets/images/sources/add-api-source.png)

!!! note
    Use **Add M3U Source** for a genuine M3U playlist URL or local `.m3u` file that is not a standard XC login link. When the link follows the standard XC `get.php?username=...&password=...` pattern, use **Add API Source** so IPTVBoss can retrieve the provider's Live, VOD, and Series categories through the API.

## Synchronize the source

1. Open **Sources** → **Sources Manager**.
2. Select the source you added.
3. Select ![](../assets/icons/ui/refresh.svg){ .ui-icon } **Sync** in the **IPTV Sources** header.
4. Wait for the synchronization to finish before editing channels or generating output.
5. Review the imported categories and channel count.

![The Sources Manager](../assets/images/sources/sources-manager.png)

If synchronization fails, confirm that the URL is reachable, credentials are correct, and the provider is online. Then review [Common Problems](../troubleshooting/common-problems.md).

!!! note "Basic and Pro"
    Free accounts have a limited number of playlist sources. 💲 Pro accounts provide higher or unlimited source limits depending on the subscription tier. See [Free vs Pro](../getting-started/free-vs-pro.md).

!!! note
    Menu labels and screenshots may change between desktop releases.
