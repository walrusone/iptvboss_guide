# AI Settings

AI Settings configure the provider and model used by IPTVBoss features that request AI-assisted output, such as sports summaries or regex assistance when those features are available.

## Configure an AI provider

1. Open **Settings**.
2. Select **AI Settings**.
3. Choose an **AI Provider**.
4. Enter the provider API key.
5. Select a **Model**, or select **Refresh Models** after entering the key.
6. Select **OK** to save.

![AI Settings](../assets/images/settings-ai.png)

!!! warning
    API keys are sensitive credentials. Store them only in the application settings and never include them in screenshots, logs, or support requests.

## Troubleshoot model loading

If the model list cannot be refreshed:

1. Confirm that the selected provider is correct.
2. Confirm that the API key is valid and has permission to list or use models.
3. Check the network connection.
4. Review the IPTVBoss logs for the provider error.
5. Select a saved or default model when the provider catalog is unavailable.

AI-generated text is optional content. Verify event summaries, predictions, or regex suggestions before publishing them to users.

!!! note "Draft review"
    Confirm which AI providers, models, and features are officially supported before publishing a provider-specific recommendation.
