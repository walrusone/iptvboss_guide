# Email Notifications

<span class="pro-badge">PRO</span> [See Free vs Pro](../getting-started/free-vs-pro.md).

Email Notification Settings can send operational notifications such as provider-user credential expiry warnings. This is an optional [Pro feature](../getting-started/free-vs-pro.md).

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

Credential-expiry checks run during standalone NoGUI synchronization and internal XC Server syncs. They can use saved expiry information even when a provider metadata refresh is not due. User notices and the manager summary follow their configured notification settings and existing notice intervals.

Email delivery failures appear in the logs and error reporting. A failed send is not recorded as a successfully sent notice, so it can be retried on a later check. If notices are missing, verify SMTP connectivity and authentication as well as the saved credential expiry and notification settings.

Credential-expiry notification does not mean that every synchronization or output failure sends email.
