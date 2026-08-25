# Advanced Source Tools

<span class="pro-badge">PRO</span> [See Free vs Pro](../getting-started/free-vs-pro.md).

The **Sources** menu contains advanced maintenance tools for imported sources and discovered tags.

![Sources menu](../assets/images/sources-menu.png)

## Remove provider prefixes

**Prefix Auto-Removal** can help normalize provider channel names before mapping and sorting.

1. Open **Sources** → **Prefix Auto-Removal**.
2. Review the detected or configured prefixes.
3. Select only prefixes that should be removed from the imported names.
4. Apply the change.
5. Recheck channel mappings and output names.

![Prefix Auto-Removal](../assets/images/sources/prefix-auto-removal.png)

!!! warning
    Name cleanup can affect matching, AED regexes, and output. Record the original naming pattern before applying a broad change.

## Manage new tags

Use **Sources** → **Manage New Tags** to review tags discovered during source or EPG processing.

![Manage New Tags](../assets/images/sources/manage-new-tags.png)

Confirm the tag behavior before applying it globally. Tags can affect grouping, filtering, or output depending on the configuration.
