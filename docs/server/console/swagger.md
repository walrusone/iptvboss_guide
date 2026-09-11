# Server Console: Swagger API Documentation

--8<-- "includes/xc-server-preview.md"

![Swagger API documentation](../../assets/images/server-console/server-swagger.png)

The server exposes interactive Swagger documentation for the external API at:

```text
https://server.example/swagger
```

After signing in to the console, open the **Swagger** link or the server's `/swagger` path. The documentation session is separate from an external automation API key; use a valid key when testing an endpoint that requires one.

The documented API includes user metadata, user listing and mutations, layout-specific password set/regenerate operations, and asynchronous operation status. The former bulk user-password reset endpoint is retained in the schema as retired and returns `410 Gone`. Treat the **Try it out** controls as live operations: use a test user and confirm the target server before sending a request.

## Layout password endpoints

Use an API key with the user-write scope and an `Idempotency-Key` for each mutation:

```http
PUT /api/v1/users/{id}/layouts/{layoutId}/password
Content-Type: application/json
Idempotency-Key: unique-operation-id

{"password":"Imported_42"}
```

To generate a new password for one assigned XC-enabled layout, call:

```http
POST /api/v1/users/{id}/layouts/{layoutId}/password/regenerate
Idempotency-Key: unique-operation-id
```

User create and patch requests can also include `layoutPasswords` entries. Each entry must reference an assigned XC-enabled layout, and passwords must be unique within that user. Passwords accept 6–64 letters, digits, or `- . _ ~`.
