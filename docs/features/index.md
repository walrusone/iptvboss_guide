# Sports & AEDs

<span class="pro-badge">PRO</span> [See Free vs Pro](../getting-started/free-vs-pro.md).

Sports workflows match provider channels to events, then control how those channels appear in a layout and its output.

```text
Source channel → AED matching → Group filtering and sorting → Presentation → Output
```

## Choose your task

| Task | Guide |
| --- | --- |
| Choose league-based or regex-based event matching | [Advanced EPG Dummies](aed.md) |
| Configure placeholders, regex, dates, times, and logos | [AED Templates and Regex](aed-reference.md) |
| Test, refresh, resume pending work, or update several AEDs | [Manage and Refresh AEDs](aed-maintenance.md) |
| Find definitions and see which layouts use them | [Dummy Guides and Inventory](../setup/dummy-guides.md) |
| Filter events, prioritize teams, and number sports channels | [Custom Sports Groups](custom-sports.md) |
| Supply fixture text for numbered provider channels | [TXT Fixture Names for AEDs](../setup/custom-sports-channel-names.md) |

A TXT file is optional: use it when the provider channel name needs additional fixture information for AED matching. AI assistance is also optional; [AI Settings](../settings/ai.md) configures the provider used by regex suggestions.

After changing matching or presentation, refresh the affected AEDs, inspect the layout, and [generate output](../setup/output.md). For missing events or interrupted refreshes, see [AED Troubleshooting](../troubleshooting/aed.md).
