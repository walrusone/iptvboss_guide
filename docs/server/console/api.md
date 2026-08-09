# Server Console: API Keys

![Server Console API keys](../../assets/images/server-console/server-api.png)

The **API Keys** section contains the key used for XC Server reload operations and named keys for scripts that call the documented `/api/v1` endpoints.

## XC Reload API Key

Use **Rotate Key** when the existing reload key may have been exposed or must be replaced. Copy the new secret immediately; it is not shown again. Update every trusted client that uses the old key before relying on reload operations.

## External Automation Keys

1. Select **Create Automation Key**.
2. Enter a descriptive name and, when appropriate, an expiry date.
3. Select the required permissions or scopes.
4. Create the key and copy the secret immediately.
5. Store it in the script or secret manager that will use it.

The console shows key metadata, but not the complete secret after creation. Rotate a key when it may be compromised and revoke keys that are no longer needed. Do not put an API key in screenshots, source control, or support requests.

The **Automation Operations** area shows pending or failed journaled mutations. Use the operation status endpoint or the console status before retrying a request.
