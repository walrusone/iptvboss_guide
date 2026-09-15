# EPG Formatting and Overrides

<span class="pro-badge">PRO</span> [See Free vs Pro](../getting-started/free-vs-pro.md).

EPG formatting controls how imported programme information is written to XMLTV output. It does not select which EPG channel is mapped to a playlist channel. For assignment, use [Mapping Channels](channel-mapping.md).

## Configure the EPG Layout

The **EPG Layout** section controls how imported programme components are combined when IPTVBoss writes the output guide. It is available while adding or editing an EPG source.

The editor contains three list views:

- **Title** — components used to create the programme title.
- **Description** — components included in the programme description.
- **Remove Components** — original components removed from the output XML after the title and description are created.

The available components are displayed above the lists. Drag a component into the required list and drop it in the order you want it to appear. For example, place **Title**, **Subtitle**, and **Movie Year** in the Title list, and place **Description** and **Category** in the Description list. Use **Next Line** or punctuation components when the output needs separators or line breaks.

To remove a component from one of the three lists, select it and right-click it. The component is removed from that list; it is not deleted from the source data. Components placed in **Remove Components** are omitted from the generated XML output, which can reduce duplicate or unwanted metadata.

!!! note
    Selecting a source from the **Copy Layout** dropdown loads that source's existing Title, Description, and Remove Components arrangement as a starting point. Review the copied layout before saving.

Save the EPG source after reviewing the three lists, then synchronize the source and inspect the generated guide output.

## Override formatting for one layout

1. Open **Layout Manager** and select the layout.
2. Expand **Output & Sync** and enable EPG output if it is not already enabled.
3. Open **EPG Layout Override** and configure the Title, Description, and Remove Components lists.
4. Enable **Override EPG Layout Settings** and save the layout.
5. Generate that layout's EPG and inspect representative programmes in the output.

With Pro active, an enabled layout override supplies all three component lists for that layout's output. Otherwise the EPG source's component lists are used. The lists are selected as a set, not merged: include every component you want in the override.

For example, a source can use Title + Subtitle while one layout uses Title alone. Enable the override only on that layout. Output without a current layout, such as source-level guide output, uses the source configuration.

## Verify formatting

Check programmes with and without optional fields such as subtitles or movie years. Confirm the title, description, separators, and retained XML components. Regenerate output and refresh the player after changing formatting.

For delivery formats and destinations, see [Layout Output Settings](../layouts/output-settings.md). For event-generated names and descriptions, use the separate [AED template reference](../features/aed-reference.md).
