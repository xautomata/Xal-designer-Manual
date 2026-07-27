# Setup — API Key

Arianna requires an Anthropic API key to function. The key is stored locally in your browser and never sent to Xautomata's servers — it is used exclusively for direct calls to the Claude API.

---

## Entering the key

Click the **Wand** icon in the left sidebar to open the AI Assistant panel. If no key is configured, a prompt appears asking you to enter one.

Paste your Anthropic API key and press **Confirm**. The key is validated immediately; if the key is invalid or has insufficient permissions, an error message is shown.

![API key prompt in the AI Assistant panel](../../images/designer/arianna/setup_api_key.png)
/// caption
Fig.1 - The API key entry prompt (screenshot pending)
///

---

## Removing the key

To clear the stored key — for example, when logging out of a shared machine — open the AI Assistant panel and click **Remove key**. This logs you out of Arianna and removes all chat sessions linked to repository files. Sessions linked to locally uploaded files are preserved.

---

!!! warning "Key security"
    Your API key is stored in your browser's local storage. Do not use Arianna on a shared or public computer without removing the key when you are done.
