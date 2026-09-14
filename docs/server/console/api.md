# Server Console: API Keys

--8<-- "includes/xc-server-preview.md"

![Server Console API keys](../../assets/images/server-console/server-api.png)

The **API Keys** section lists the reload-only credentials created for paired IPTVBoss clients and the named keys used by scripts that call the documented `/api/v1` endpoints. These are separate credential types with different permissions.

## Paired reload credentials

The server creates a client-specific reload credential automatically during pairing. The desktop client uses it to request a server reload after its shutdown backup workflow completes. It cannot be used for general administration or external automation.

Manage this access through [Paired Devices](paired-devices.md). Revoking a paired client also revokes its reload credential. If a credential is rejected but the pairing is still valid, the client attempts to refresh the credential and retry once. Normally, users do not need to copy or configure these credentials manually.

See [Automatic server reloads from this client](../gui-settings.md#automatic-server-reloads-from-this-client) for the user-facing workflow.

## External Automation Keys

1. Select **Create Automation Key**.
2. Enter a descriptive name and, when appropriate, an expiry date.
3. Select the required permissions or scopes.
4. Create the key and copy the secret immediately.
5. Store it in the script or secret manager that will use it.

The console shows key metadata, but not the complete secret after creation. Rotate a key when it may be compromised and revoke keys that are no longer needed. Do not put an API key in screenshots, source control, or support requests.

The **Automation Operations** area shows pending or failed journaled mutations. Use the operation status endpoint or the console status before retrying a request.

## Access the OpenAPI specification

External API keys can retrieve the raw OpenAPI JSON (`openapi.json`) document from:

```text
https://server.example/openapi
```

Send the key in the `X-IPTVBoss-Api-Key` header:

```bash
curl -H 'X-IPTVBoss-Api-Key: YOUR_KEY' \
  https://server.example/openapi
```

The specification endpoint accepts a valid, non-revoked external automation key and does not require a user scope. API-key rate limits and access auditing still apply. An authenticated administrator console session can also access the specification.

The interactive `/swagger` UI and its web assets remain administrator-session-only; an API key by itself does not sign in to the console or open Swagger UI.

The current specification documents user metadata, user listing and detail, create/patch/delete, asynchronous operation status, and layout-specific password set/regenerate operations. The former `POST /api/v1/users/{id}/reset-password` route remains documented as retired and returns `410 Gone`; use the layout-specific routes instead.
