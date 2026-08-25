# Direct HTTPS

Direct HTTPS makes IPTVBoss terminate TLS itself. Use it when an HTTPS reverse proxy cannot be used. The XC Server normally listens at `https://host:8001` and requires a PKCS#12 certificate store named `keystore.p12` in the selected IPTVBoss data directory.

!!! tip "A reverse proxy is normally easier"
    [Caddy and other HTTPS reverse proxies](index.md) manage public certificates and automatic renewal without giving IPTVBoss the TLS private key. Proxy mode does not load a local keystore and is the recommended option for Internet-facing installations.

!!! warning "There is no HTTP fallback"
    Direct HTTPS starts a TLS-only listener. It does not also open HTTP or redirect HTTP requests. Do not combine `-xc-proxy` with direct HTTPS; proxy mode takes precedence and leaves the keystore unused.

## What the keystore password protects

`keystore.p12` contains the TLS private key, server certificate, and certificate chain used for HTTPS connections. `IPTVBOSS_XC_KEYSTORE_PASSWORD` unlocks that store and its private key when XC Server starts.

The variable does **not** encrypt the IPTVBoss database and does not protect XC users, administrator credentials, section PINs, or API keys. The password must match the export password used when `keystore.p12` was created.

Protect both the file and the password. Anyone who obtains both can impersonate the HTTPS server until the certificate is revoked or expires.

Generate a strong value that is also safe to copy into systemd and Compose environment files:

```bash
openssl rand -hex 32
```

Save the generated value in a password manager and enter it when the certificate tool requests the keystore or export password. Do not add the password itself to commands, shell history, or source control.

## Prepare the certificate name

Choose the exact hostname or IP address that every client will use, such as `boss.example.com` or `boss.lan`. The certificate Subject Alternative Name (SAN) must contain that value. A certificate for `boss.lan` will still fail validation when a client connects to `192.168.1.50` unless the IP address is also present in the SAN.

Use one of the following certificate workflows.

The certificate can be prepared on another trusted computer and then copied to the server. The CA-issued workflow requires OpenSSL. The self-signed workflow requires `keytool` from a Java Development Kit; the Java runtime bundled with IPTVBoss may not expose that utility.

### Convert a CA-issued certificate

If a public or private certificate authority supplied a PEM private key, server certificate, and chain, create the PKCS#12 store with OpenSSL:

```bash
openssl pkcs12 -export \
  -name iptvboss \
  -inkey privkey.pem \
  -in certificate.pem \
  -certfile chain.pem \
  -out keystore.p12
```

OpenSSL prompts for the export password. Use that same value for `IPTVBOSS_XC_KEYSTORE_PASSWORD`. If the CA supplied a `fullchain.pem` containing the server certificate followed by its chain, use it with `-in fullchain.pem` and omit `-certfile chain.pem`.

Do not copy the unencrypted PEM private key into the IPTVBoss data directory. Store or remove the source files according to the certificate authority's renewal procedure.

### Create a private-LAN self-signed certificate

Self-signed certificates are suitable only when you control every client and can install the certificate as trusted. Replace the example hostname and IP address before running:

```bash
keytool -genkeypair \
  -alias iptvboss \
  -keyalg RSA \
  -keysize 3072 \
  -sigalg SHA256withRSA \
  -validity 365 \
  -storetype PKCS12 \
  -keystore keystore.p12 \
  -dname "CN=boss.lan" \
  -ext "SAN=dns:boss.lan,ip:192.168.1.50" \
  -ext "EKU=serverAuth"
```

`keytool` prompts for the keystore password instead of placing it in shell history. Export the public certificate that clients must trust:

```bash
keytool -exportcert -rfc \
  -alias iptvboss \
  -keystore keystore.p12 \
  -file iptvboss-self-signed.crt
```

Install `iptvboss-self-signed.crt` in the trusted root store of every browser, desktop, phone, television, or IPTV application that connects directly. A browser exception does not make non-browser IPTV clients trust the certificate. If a client cannot install a private trust anchor, use a publicly trusted certificate or an HTTPS reverse proxy.

## Inspect the store

Confirm that the store type is `PKCS12`, it contains a `PrivateKeyEntry`, and the certificate SAN matches the connection address:

```bash
keytool -list -v -storetype PKCS12 -keystore keystore.p12
openssl pkcs12 -info -noout -in keystore.p12
```

Both commands prompt for the password. IPTVBoss refuses to start if the file is missing, cannot be opened with the configured password, or does not contain a private key and certificate chain.

## Configure a native installation

Stop XC Server before replacing its certificate. Copy the store into the same data directory used by IPTVBoss and restrict it to the service account. This example uses `/srv/iptvboss` and the account `ubuntu`:

```bash
sudo install -o ubuntu -g ubuntu -m 0600 keystore.p12 /srv/iptvboss/keystore.p12
```

For an interactive test, read the password without echoing it and start direct HTTPS:

```bash
read -rsp 'Keystore password: ' IPTVBOSS_XC_KEYSTORE_PASSWORD
echo
export IPTVBOSS_XC_KEYSTORE_PASSWORD
iptvboss -xcserver -directory /srv/iptvboss -httpsOnly -xc-bind-address all
```

For systemd, store the variable in a root-owned environment file:

```bash
sudo install -d -o root -g root -m 0700 /etc/iptvboss
sudo install -o root -g root -m 0600 /dev/null /etc/iptvboss/xc-https.env
sudoedit /etc/iptvboss/xc-https.env
```

Add this line in the environment file:

```env
IPTVBOSS_XC_KEYSTORE_PASSWORD=replace-with-the-keystore-password
```

Then use the environment file and direct-HTTPS flags in the service:

```ini
[Service]
User=ubuntu
EnvironmentFile=/etc/iptvboss/xc-https.env
ExecStart=/usr/bin/iptvboss -xcserver -directory /srv/iptvboss -httpsOnly -xc-bind-address all
```

Remove `-xc-proxy` from the service. Reload and restart it:

```bash
sudo systemctl daemon-reload
sudo systemctl restart iptvboss.service
sudo systemctl status iptvboss.service
```

For launchd on macOS, make the equivalent changes in `pro.iptvboss.xcserver.plist`: replace `-xc-proxy` with `-httpsOnly`, change the bind value from `loopback` to `all`, and add `IPTVBOSS_XC_KEYSTORE_PASSWORD` under `EnvironmentVariables`. Because the plist then contains a password, install it as `root:wheel` with mode `0600`. The data directory must contain `keystore.p12` and remain readable by the configured `UserName`.

## Configure Docker

Set direct HTTPS and publish the default port in `.env`:

```env
IPTVBOSS_XC_BEHIND_HTTPS_PROXY=false
IPTVBOSS_HTTPS_ONLY=true
IPTVBOSS_XC_BIND_ADDRESS=all
IPTVBOSS_XC_KEYSTORE_PASSWORD=replace-with-the-keystore-password
IPTVBOSS_HOST_IP=0.0.0.0
IPTVBOSS_HOST_PORT=8001
```

The default Compose file contains only IPTVBoss. Direct HTTPS and reverse-proxy mode must not be enabled together.

Restrict `.env` and ensure it is excluded from source control:

```bash
chmod 0600 .env
```

Create `compose.direct-https.yaml` beside the normal Compose file. The long bind-mount syntax prevents a missing source file from being silently created as a directory:

```yaml
services:
  iptvboss:
    volumes:
      - type: bind
        source: ./keystore.p12
        target: /data/keystore.p12
        read_only: true
        bind:
          create_host_path: false
```

On Linux, make the file readable only by the UID/GID configured for the container. The templates default to `10001:10001`:

```bash
sudo chown 10001:10001 keystore.p12
sudo chmod 0400 keystore.p12
```

Use the customized `IPTVBOSS_UID` and `IPTVBOSS_GID` values instead when they differ. Start and inspect the direct-HTTPS deployment:

```bash
docker compose -f compose.yaml -f compose.direct-https.yaml config
docker compose -f compose.yaml -f compose.direct-https.yaml up --detach
docker compose logs --follow iptvboss
```

The Docker health check supports HTTPS on the internal port. Publishing host port `443` instead of `8001` is possible by setting `IPTVBOSS_HOST_PORT=443`; clients can then omit `:8001` from the URL.

## Verify HTTPS

For a publicly trusted certificate:

```bash
curl --fail https://boss.example.com:8001/healthz
openssl s_client -connect boss.example.com:8001 -servername boss.example.com </dev/null
```

For the self-signed example, verify with its exported certificate:

```bash
curl --fail --cacert iptvboss-self-signed.crt https://boss.lan:8001/healthz
```

Open `https://boss.example.com:8001/boss.php` or the matching private-LAN address after the health check succeeds. Configure IPTVBoss's public server URL and **Include Port in URL** setting to match the address clients actually use.

## Renew or replace the certificate

IPTVBoss does not issue, renew, or hot-reload certificates. Before the existing certificate expires:

1. Obtain the renewed certificate and chain.
2. Create a new `keystore.p12` with its matching private key.
3. Confirm its contents and SAN with `keytool -list -v`.
4. Stop XC Server, replace the old store while preserving its ownership and permissions, and restart the service or container.
5. Repeat the HTTPS health check from another machine.

For frequently renewed public certificates, prefer a reverse proxy that automates issuance and reloads.

## Troubleshooting

| Symptom | Cause and action |
| --- | --- |
| Startup says `keystore.p12` is required | The file is not in the selected IPTVBoss data directory, or the Docker bind mount is missing. |
| Startup says the password does not unlock the store | `IPTVBOSS_XC_KEYSTORE_PASSWORD` differs from the PKCS#12 export password. Re-enter the matching value. |
| Startup says the store is invalid or lacks a private key | Recreate it as PKCS#12 with the server certificate, matching private key, and complete chain. A certificate-only trust store is insufficient. |
| Browser or client reports a name mismatch | Connect using a DNS name or IP address included in the certificate SAN, or reissue the certificate with the correct SAN. |
| Browser trusts the certificate but an IPTV client does not | Install the private certificate in that client or use a publicly trusted certificate. Some clients do not support private trust anchors. |
| HTTP requests fail or show TLS errors | Use `https://`; direct HTTPS does not open an HTTP listener. |
| Direct HTTPS unexpectedly starts HTTP | Disable `IPTVBOSS_XC_BEHIND_HTTPS_PROXY` and remove `-xc-proxy`; proxy mode takes precedence. |
| The renewed certificate is not served | Restart XC Server after replacing `keystore.p12`. |

See the official [Java `keytool` documentation](https://docs.oracle.com/en/java/javase/21/docs/specs/man/keytool.html), [OpenSSL PKCS#12 documentation](https://docs.openssl.org/3.3/man1/openssl-pkcs12/), and [Docker bind-mount documentation](https://docs.docker.com/engine/storage/bind-mounts/) for the underlying tools.
