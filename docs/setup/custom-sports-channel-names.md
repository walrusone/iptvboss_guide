# Custom Sports Channel Names for AEDs

<span class="pro-badge">PRO</span> [See Free vs Pro](../getting-started/free-vs-pro.md).

**Custom Sports Channel Names** lets a source use a local or hosted TXT file of fixture names when an AED needs a more descriptive channel name to find the correct event. The TXT name is used as an AED lookup name; it does not rename the channel displayed in your layout.

This is useful when a provider supplies stable numbered sports channels such as `ESPN 1`, but the sports data or AED needs the current fixture name, such as `ESPN 1: Lakers vs Celtics`.

!!! note
    Custom Sports Channel Names is configured on an individual playlist source. If several sources need custom fixture names, configure a file for each source.

## How the lookup works

The source channel name identifies a channel prefix. For a source channel named:

```text
ESPN 1
```

the matching TXT file can contain a current fixture such as:

```text
ESPN 1: Lakers vs Celtics
```

When IPTVBoss processes an AED assigned to that channel, it can use the matching TXT line as the temporary lookup name. The AED then evaluates that fixture name against its league/event or regex rules. The original channel name remains available for output and fallback behavior.

The matching is deliberately prefix-based:

- The channel must begin with a prefix followed by a space and a number, such as `ESPN 1` or `Sportsnet 4`.
- A TXT line must begin with the complete source channel name.
- The next character after the source channel name must not be a letter or number. A colon, hyphen, or space is a good separator.
- The first matching TXT line is used as the primary custom lookup name. Multiple matching lines may also be considered by sports matching.

For example, a source channel named `ESPN 1` matches `ESPN 1: Lakers vs Celtics`, but does not match `ESPN 10: Other Event` because the next character is a number.

## Create the TXT file

Create a plain UTF-8 text file, for example `fixtures.txt`. Use one complete fixture/channel name per line:

```text
ESPN 1: Lakers vs Celtics
ESPN 2 - Bruins vs Canadiens
Sportsnet 1 | Toronto FC vs Montreal
DAZN 3: UFC Fight Night
```

Follow these rules:

- Use one fixture per line.
- Begin each line with the exact provider channel name, including its number.
- Add a separator after the channel name, such as `:`, ` - `, or ` | `.
- Keep the fixture text in the form that the AED and sports data can recognize.
- Do not add a header such as `fixtures.txt` or `channel=fixture`.
- Blank lines are harmless, but avoid explanatory text that does not begin with a valid numbered channel prefix.
- Save the file as UTF-8 so team and league names with accented characters load correctly.

The file is a current lookup list, not a permanent archive. Replace or regenerate its fixture lines as events change.

## Add the file while adding a playlist

The file location is configured in the playlist source dialog, not in the AED editor.

### M3U source

1. Open **Sources** → **Add M3U Source**, or edit an existing M3U source.
2. Find **Custom Sports Channel Names**.
3. Enter the full local path to `fixtures.txt`, or enter an `https://`/`http://` URL that returns the plain text file.
4. Select **Browse** when the file is stored locally.
5. Save the source and complete [Playlist Categories](playlist-categories.md) if this is a new source.

### Xtream Codes source

1. Open **Sources** → **Add API Source**, or edit an existing Xtream Codes source.
2. Find **Custom Sports Channel Names**.
3. Enter the local path or hosted TXT URL.
4. Save the source and synchronize it if the source or its categories have changed.

The field stores either a local path or a URL. IPTVBoss downloads a hosted file when it refreshes the custom-name cache; it does not expect a web page containing the text. The URL should return the raw TXT contents directly.

## Connect the names to an AED

1. Make sure the source channel name and the first part of each TXT line match.
2. Add or edit an AED under **Sources** → **AED Editor**.
3. Configure the AED's sport/league matching or regex fields for the fixture text.
4. Assign the AED to the appropriate layout channel or group through the normal AED workflow.
5. Test the AED using a representative TXT fixture line.
6. Save the AED and refresh the assigned AED channels.

The TXT file does not assign an AED by itself. It only supplies an alternate lookup name for channels that already use an AED. The AED still needs the correct matching configuration and assignment.

## Refresh after changing the file

After replacing the local file or updating the hosted file:

1. Open **Sources**.
2. Select **Reload TXT Channel Names**. This reloads the configured files for all sources.
3. Refresh the assigned AED channels, or select **Refresh All AEDs** when several AEDs depend on the file.
4. Generate or preview the affected layout output.

The file is also loaded during normal source/AED processing. Use the explicit reload action when you need to test a changed file immediately without restarting IPTVBoss.

## Troubleshooting

If an AED does not use the fixture name, check the following:

- The TXT path or URL is correct and readable from the computer running IPTVBoss.
- A hosted URL returns plain text rather than an HTML login page or download page.
- The line starts with the complete provider channel name, including the numeric suffix.
- The separator after the channel name is not a letter or number.
- The file is saved as UTF-8.
- The AED is assigned to the channel and its matching rules fit the fixture text.
- **Reload TXT Channel Names** was run after the file changed.
- The AED channel was refreshed after the custom lookup name was loaded.

Check the IPTVBoss log for custom sports TXT load failures. A missing file, failed download, or invalid URL prevents the lookup cache from being populated; it does not change the underlying provider channel name.
