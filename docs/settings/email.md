# Email Notifications

💲 [Pro feature — see Free vs Pro](../getting-started/free-vs-pro.md).

Email Notification Settings can send operational notifications such as provider-user credential expiry warnings. This is an optional 💲 [Pro feature](../getting-started/free-vs-pro.md).

## Configure SMTP

1. Open **Settings**.
2. Select **Email Notification Settings**.
3. Enter the SMTP server and port supplied by the email provider.
4. Enter the sender or account information required by the provider.
5. Enter the recipient address for notifications.
6. Choose the expiry-warning period when available.
7. Save the settings.
8. Send a test email.

!!! warning
    SMTP passwords, application passwords, and email credentials are sensitive. Do not include them in screenshots, logs, or support requests.

## Verify notifications

1. Confirm that the test email arrives.
2. Confirm that the sender and recipient are correct.
3. Review the IPTVBoss logs if the test fails.
4. Confirm that the relevant provider user has an expiry date that can be monitored.

The exact notification events and timing may vary by release. The current documented purpose is credential-expiry notification; do not assume that every synchronization or output failure sends email.
