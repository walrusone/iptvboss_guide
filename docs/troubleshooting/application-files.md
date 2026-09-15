# Application Files

Use these defaults to locate the installation data. A configured `-directory` override or a server service may use another location; check the [runtime settings](../server/runtime.md#data-and-port-defaults) or the service definition.

## Application folder locations

| Operating system | Default location |
| --- | --- |
| Windows | `C:\Users\{user}\IPTVBoss` |
| Linux | `/home/{user}/IPTVBoss` |
| macOS | `~/Library/Application Support/IPTVBoss/` |

When Windows and headless Linux use the same synchronized installation data, the Windows drive path may appear under the Linux application folder, for example:

```text
/home/{user}/IPTVBoss/C:
```

If the Windows installation uses another drive or custom path, use the corresponding folder name instead of `C:`.

The `db` and `backup` directories are especially important. Do not delete them until you have copied them somewhere safe and confirmed that the copy can be read.
