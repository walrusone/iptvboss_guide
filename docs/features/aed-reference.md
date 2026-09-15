# AED Templates and Regex

<span class="pro-badge">PRO</span> [See Free vs Pro](../getting-started/free-vs-pro.md).

Start with [Advanced EPG Dummies](aed.md) to choose an AED approach. Use this reference while configuring output fields, then [test and refresh the results](aed-maintenance.md#test-and-refresh-an-aed).

## Output placeholders

Use these placeholders in AED output fields. A value may be empty when the source or event does not provide it.

| Placeholder | Meaning |
| --- | --- |
| `{title}` | Full event title and the first title-regex capture group |
| `{title2}`–`{title10}` | Additional title-regex capture groups |
| `{team1}`, `{team2}` | Home and away team names |
| `{team1short}`, `{team2short}` | Short home and away team names |
| `{team1abbr}`, `{team2abbr}` | Team abbreviations |
| `{team1record}`, `{team2record}` | Team records, such as `12-3` |
| `{team1nick}`, `{team2nick}` | Team nicknames or mascots |
| `{team1loc}`, `{team2loc}` | Team city or location |
| `{prefix}` | Custom Sports group presentation name and number, such as `ESPN+ 001` (Channel Name Regex only) |
| `{league}` | League Display Name, falling back to Name when Display Name is empty or null |
| `{leagueabbr}` | League abbreviation |
| `{leagueshort}` | Short league name |
| `{arena}`, `{city}`, `{state}` | Venue and location information |
| `{time}` | Event start time |
| `{countdown}` | Countdown such as `5 hours 30 minutes` |
| `{Countdown}` | Sentence-case countdown |
| `{livetag}` | Live-event indicator tag |
| `{newline}` | A line break in output text |
| `{summary}` | AI-generated event preview, when available |
| `{predictor}` | Matchup prediction, when available |
| `{oddsml}` | Moneyline odds, when available |

`{title1}` is an alias for `{title}`. In Sports AEDs and Title Regex, `{title}` is the event title, so additional regex groups begin at `{title2}`. In a Simple Regex Dummy, capture group 1 maps to `{title}` and group 2 maps to `{title2}`.

## Conditional output

Use an `if` block to hide text when one of its placeholders has no value:

```text
{if - {title2}endif}
```

This is useful for an optional subtitle. To add punctuation only when the value exists, include it inside the conditional, for example:

```text
{if({title2})endif}
```

!!! warning
    Do not put `{newline}` inside an `{if}` block. It can cause the entire block to be ignored.

Use an `or` block to select the first branch that has a value:

```text
{livetag} {or{team1} at {team2}{||}{title}endor}
```

This produces a team matchup when team values exist and falls back to the event title otherwise. Nested `{or}` and `{if}` blocks can handle sports matchups, titled events with subtitles, and title-only events in one template.

## Sports logos

In the AED Logo field for Sports EPGs, use these placeholders to select a supplied sports graphic:

| Placeholder | Selects |
| --- | --- |
| `{logo}` | Universal choice; automatically selects a team or event logo |
| `{event}` | A matchup graphic for the two teams |
| `{team}` | A single team badge |
| `{league}` | A league logo |
| `{sport}` | A sport-level logo |

Use `{logo}` when you do not need to distinguish between team and matchup artwork.

For bulk logo assignment, numbered placeholders expand sequentially:

| Placeholder | Sequence |
| --- | --- |
| `{num1}` | `001`, `002`, `003` |
| `{nu1}` | `01`, `02`, `03` |
| `{n1}` | `1`, `2`, `3` |

For example:

```text
https://cdn.iptvboss.pro/logos/USA/ESPN+{num1}.v.png
```

Select the channels in a group, open the bulk logo editor, and paste a URL containing the numbered placeholder. IPTVBoss assigns the next number to each selected channel.

## Regex fields

### Channel Name Regex

Channel Name Regex controls which provider channels match the AED and can also create a clean output name.

- `{time}` and `{countdown}` are not supported in this field.
- `{league}` and `{leagueabbr}` need another placeholder alongside them; do not use either one by itself.
- When no event is active, the provider channel name is used as the fallback.
- In a Custom Sports group, `{prefix}` resolves to the group presentation name and the channel’s final numbered position, such as `ESPN+ 008`.
- For a team-based channel, `{title}` and `{shorttitle}` resolve to that channel’s team name in this field. They continue to behave normally in the other AED output fields.
- Missing league, sport, or other optional values resolve to an empty string. Put surrounding punctuation in an `{if}` block when it should disappear with the missing value.

Custom Sports TXT names are lookup inputs and do not replace the universal channel name by themselves. When a configured Channel Name Regex is blank, invalid, or renders no value, the channel's own name is retained.

Examples:

```text
{leagueabbr}: {title}
```

This can produce:

```text
NHL: Detroit Red Wings at Chicago Blackhawks
```

For a Custom Sports group with the presentation name ESPN+, this template can include the numbered group presentation:

    {prefix}: {shorttitle}

It can produce:

    ESPN+ 008: Real Betis

`{prefix}` is specific to **Channel Name Regex**. It does not change the meaning of `{title}` or `{shorttitle}` in the AED Title, Description, or other output fields.

If the AED is used for one league only, a fixed prefix is also possible:

```text
NHL: {title}
```

To keep only the provider prefix from a replay channel, this pattern matches the text through `NFL`:

```text
^(.*?\|\s*NFL)
```

### Title Regex

Each capture group in Title Regex maps to a title placeholder: the first group is `{title}`, the second is `{title2}`, and so on.

For a pipe-delimited provider name such as:

```text
Channel: AU (STAN 12) | Nice v Roma _ UEFA Europa League 2025/2026 (2025-09-25 04:55:43)
```

this pattern extracts the matchup and competition:

```regex
^[^\|]*\|\s*([^_]+?)\s*_\s*([^()]+?)\s*\(
```

The captures are `{title} = Nice v Roma` and `{title2} = UEFA Europa League 2025/2026`.

When a provider may omit one of its delimiters, a lookahead-based pattern can be more tolerant:

```regex
(?:.*?\|\s*)(.*?)(?=\s*[_\(])|(?:.*?\_\s*)(.*?)(?=\s*[_\/\(])
```

For FloSports-style names, separate patterns can extract the event and sport portions:

```regex
:\s*\d{4}\s+(.*?)\s*-
-\s*(.*?)\s*-\s*\d{2}/\d{2}
```

### Time Regex

For times written with AM or PM, this pattern matches values such as `8pm`, `8:30pm`, and `8:30PM`:

```regex
\s\d+[:\d+]*[Aa|Pp][M|m]
```

!!! warning
    Alternation such as `regex1|regex2` can populate only one side's capture groups. Prefer named groups or separate patterns when both formats must return values.

## Time and date formats

Date and time settings tell the AED how to interpret values captured from a provider channel name. The regex finds the value; the corresponding format tells IPTVBoss what that value means.

- **Time Regex** identifies the time in the provider name.
- **Time Format** describes the captured time, including whether it uses a 12-hour or 24-hour clock and whether it includes minutes or an AM/PM marker.
- **Date Regex** identifies the date in the provider name.
- **Date Format** describes the captured date, including the order of the day, month, and year.
- **Timezone of Source** identifies the timezone represented by the provider’s date and time.
- **Output Time Format** and **Output Timezone** control how the event time is shown in the generated output.

For example, a channel containing `03 Aug 06:40 PM` could use a date format such as `d MMM` and a time format such as `h:mm a`. A provider using a 24-hour value such as `18:40` needs a matching 24-hour time format. The format must match the provider text exactly; changing the format without changing the source pattern can prevent the event from matching.

Use the AED tester with samples from different days, times, and naming variations. Check the source timezone as well as the output timezone when an event appears at the wrong time.
