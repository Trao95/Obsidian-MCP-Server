# Obsidian-MCP-Server
Seamlessly integrate Claude AI into your Obsidian vault! This guide provides a straightforward setup for the Model Context Protocol (MCP) server on Windows 11, empowering Claude to directly assist your brainstorming, notetaking, and knowledge management within Obsidian.

# Obsidian AI Integration: Setting Up an MCP Server for Claude Desktop

This guide provides step-by-step instructions for setting up an **Obsidian Model Context Protocol (MCP) server** on Windows 11. This server acts as a bridge, allowing external AI applications like **Claude Desktop** to securely read from and write to your Obsidian vault, unlocking powerful AI-driven workflows directly with your notes.

---

## Table of Contents

* [Why Use an Obsidian MCP Server?](#why-use-an-obsidian-mcp-server)
* [Prerequisites](#prerequisites)
* [Step-by-Step Setup Guide](#step-by-step-setup-guide)
    * [Phase 1: Prepare Your Obsidian Vault](#phase-1-prepare-your-obsidian-vault)
    * [Phase 2: Configure Claude Desktop to Launch the MCP Server](#phase-2-configure-claude-desktop-to-launch-the-mcp-server)
* [Troubleshooting Common Issues](#troubleshooting-common-issues)
    * [No `mcp-debug.log` file is generated](#no-mcp-debuglog-file-is-generated)
    * [`claude_desktop_config.json` Syntax Errors](#claude_desktop_configjson-syntax-errors)
    * [Only an Existing MCP Server (e.g., Blender) Appears](#only-an-existing-mcp-server-eg-blender-appears)
    * [API Key or Vault Path Errors](#api-key-or-vault-path-errors)
    * [Server Error in Logs (e.g., "Address already in use")](#server-error-in-logs-eg-address-already-in-use)
    * [General Connectivity Issues / Claude Not Picking Up Config](#general-connectivity-issues--claude-not-picking-up-config)
* [Contributing](#contributing)
* [License](#license)

---

## Why Use an Obsidian MCP Server?

The Model Context Protocol (MCP) server empowers AI models to:

* **Read Your Notes:** Access your vault's content for summarization, in-depth analysis, and contextual understanding.
* **Create and Modify Notes:** Generate new ideas, draft content, or update existing files based on your AI prompts.
* **Search Your Vault:** Perform comprehensive searches across your knowledge base to find relevant information for AI tasks, making your knowledge truly intelligent.

This integration is particularly useful when you want a standalone AI application like Claude Desktop to work directly with your knowledge base, often offering deeper and more seamless interaction than in-Obsidian plugins alone.

---

## Prerequisites

Before you start, ensure you have the following:

* **Obsidian Installed:** An active Obsidian vault you wish to integrate with AI.
* **Node.js (Version 20 or higher):** The MCP server runs on Node.js.
    * **Check your version:** Open PowerShell and run: `node -v`
    * If you don't have Node.js or it's an older version, download and install the latest LTS (Long Term Support) version from [nodejs.org](https://nodejs.org/).
* **Claude Desktop:** The AI application you'll be connecting.

---

## Step-by-Step Setup Guide (Windows 11)

Follow these instructions carefully to get your Obsidian MCP server integrated with Claude Desktop.

### Phase 1: Prepare Your Obsidian Vault

This phase sets up a crucial Obsidian community plugin that enables secure external access to your vault.

1.  **Open your Obsidian Vault:** Launch Obsidian and open the specific vault you want the AI to interact with.
2.  **Go to Settings:** Click the **gear icon** (Settings) in the bottom-left corner of the Obsidian window.
3.  **Navigate to Community Plugins:** In the settings sidebar, click on **Community plugins**.
4.  **Turn Off Restricted Mode:** If "Restricted mode" is enabled, toggle it **off**. Confirm the action if prompted.
5.  **Browse for Plugins:** Click the **Browse** button next to "Community plugins."
6.  **Search for "Local REST API":** In the search bar, type `Local REST API`.
7.  **Install the Plugin:** Find the "Local REST API" plugin (developed by `pjeby`) and click **Install**.
8.  **Enable the Plugin:** Once installed, click the **Enable** button.
9.  **Configure Local REST API & Generate API Key:**
    * In the Community plugins list, find "Local REST API."
    * Click the **gear icon** next to it (or the "Options" button) to access its settings.
    * Locate the section to **Generate new API key** and click the button.
    * **Crucially, immediately copy this generated API key.** This key is highly sensitive and grants access to your vault. Treat it like a password; do not share it publicly. You will need it in the next phase.

### Phase 2: Configure Claude Desktop to Launch the MCP Server

In this phase, you'll configure Claude Desktop to automatically launch and manage the Obsidian MCP server whenever Claude Desktop starts.

1.  **Close Claude Desktop Completely:** Ensure the application is fully closed, not just minimized to the system tray. Use Task Manager (Ctrl+Shift+Esc) if necessary, find "Claude" in the "Apps" or "Background processes" section, right-click, and select "End task."
2.  **Locate Claude Desktop's Configuration File:**
    * Open File Explorer.
    * In the address bar, type: `%APPDATA%\Claude\` and press **Enter**.
    * You should find a file named `claude_desktop_config.json` directly in this folder. If it doesn't exist, create a new plain text file with this exact name.
3.  **Edit the Configuration File:**
    * Open `claude_desktop_config.json` with a plain text editor (e.g., Notepad, VS Code, Notepad++).
    * **Important:** If you have an existing configuration (e.g., for a Blender MCP server), you'll add the Obsidian entry *alongside* it, separated by a comma.
    * **Add or modify the `mcpServers` section to include your Obsidian MCP server configuration:**

    ```json
    {
      "mcpServers": {
        "obsidian": { // You can name this anything, "obsidian" is descriptive.
          "command": "npx",
          "args": ["-y", "obsidian-mcp", "YOUR_OBSIDIAN_VAULT_PATH_HERE"],
          "env": {
            "OBSIDIAN_API_KEY": "YOUR_ACTUAL_OBSIDIAN_API_KEY_HERE"
          }
        }
        // If you have other servers (like Blender), they would be listed here,
        // separated by a comma from the "obsidian" entry, like this:
        // "anotherServerName": { ... },
        // "obsidian": { ... }
      }
    }
    ```
    * **Key points for the configuration above:**
        * Replace `YOUR_OBSIDIAN_VAULT_PATH_HERE` with the **exact, full, absolute path to your Obsidian vault folder on your system.**
            * *Example for Windows:* `"C:/Users/YourUser/Documents/MyVault"` (using forward slashes, generally recommended in JSON)
            * *Alternatively (Windows):* `"C:\\Users\\YourUser\\Documents\\MyVault"` (using double backslashes)
        * Replace `YOUR_ACTUAL_OBSIDIAN_API_KEY_HERE` with the **exact API key** you copied from Phase 1, step 9. This is a long string of characters.
    * **Save the `claude_desktop_config.json` file.**
4.  **Validate JSON Syntax:**
    * Copy the *entire content* of your modified `claude_desktop_config.json` file.
    * Go to an online JSON validator like [jsonlint.com](https://jsonlint.com/).
    * Paste your JSON into the validator and click "Validate JSON." **It *must* say "Valid JSON."** If it shows any errors (e.g., "Missing comma," "Bad string"), fix them precisely in your `claude_desktop_config.json` file and save again.
5.  **Restart Claude Desktop:** Launch Claude Desktop. Allow a minute or two for it to fully load and attempt to start the MCP server.

Once Claude Desktop starts, it should now be able to communicate with your Obsidian vault! Look for indications within Claude Desktop's UI that it recognizes the Obsidian server.

---

## Troubleshooting Common Issues

Here are solutions to common problems encountered during setup:

* **No `mcp-debug.log` file is generated:**
    * This indicates Claude Desktop isn't even attempting to launch the MCP server or process its output.
    * **Verify `claude_desktop_config.json` Location:** Ensure the file is *exactly* named `claude_desktop_config.json` and is located *directly* in `%APPDATA%\Claude\`. No subfolders.
    * **Verify `claude_desktop_config.json` Syntax:** A single syntax error (e.g., missing comma, brace, or quote) will prevent Claude from reading the entire file. Use `jsonlint.com` to validate the entire file.
    * **Perform a Hard Restart of Claude Desktop:** Use Task Manager (Ctrl+Shift+Esc) to "End task" for all Claude processes, then relaunch.

* **`claude_desktop_config.json` Syntax Errors:**
    * **Use a JSON Validator:** Always copy your entire `claude_desktop_config.json` content and paste it into `jsonlint.com` after every change. It will pinpoint exact syntax errors.
    * **Missing Commas:** If you have multiple servers in `mcpServers` (e.g., `blender` and `obsidian`), each entry (except the very last one) must be followed by a comma.

        ```json
        "server1": { ... },  <-- COMMA HERE
        "server2": { ... }
        ```

* **Only an Existing MCP Server (e.g., Blender) Appears:**
    * This happens when you've either overwritten your existing configuration or introduced a syntax error that prevents the new entry from being parsed.
    * **Ensure Proper JSON Structure:** When adding a new server, make sure it's a new key-value pair within the `mcpServers` object, separated by a comma from the preceding entry. Refer to the example in [Phase 2, step 3](#phase-2-configure-claude-desktop-to-launch-the-mcp-server).

* **API Key or Vault Path Errors:**
    * Even if `mcp-debug.log` is generated, these errors will often appear within it.
    * **API Key:** Double-check that the `OBSIDIAN_API_KEY` in your `claude_desktop_config.json` exactly matches the key generated in Obsidian's "Local REST API" plugin settings. Ensure there are no extra spaces or missed characters during copy-pasting.
    * **Vault Path:** Verify the path `YOUR_OBSIDIAN_VAULT_PATH_HERE` is the **exact, absolute path** to your Obsidian vault folder. Confirm correct slash usage: `C:/path/to/vault` (forward slashes) or `C:\\path\\to\\vault` (double backslashes) in JSON strings.

* **Server Error in Logs (e.g., "Address already in use"):**
    * If `mcp-debug.log` shows the MCP server attempting to start but failing with an error like "Address already in use" on port `27123`:
    * Another application (or a previous instance of the MCP server) is already using that port.
    * **Solution:** You can try to configure the `obsidian-mcp` server to use a different port.
        * In `claude_desktop_config.json`, modify the `args` array for the `obsidian` server:
            ```json
            "args": ["-y", "obsidian-mcp", "YOUR_OBSIDIAN_VAULT_PATH_HERE", "--port", "27124"],
            ```
        * Then, perform a hard restart of Claude Desktop.

* **General Connectivity Issues / Claude Not Picking Up Config:**
    * If everything else seems correct but Claude still doesn't recognize the server:
    * **Hard Restart Claude Desktop:** As discovered during troubleshooting, sometimes a standard restart isn't enough. Use Task Manager (Ctrl+Shift+Esc) to ensure all "Claude" processes are ended before relaunching.
    * **Reinstall Claude Desktop (Last Resort):** If all else fails, consider uninstalling and reinstalling Claude Desktop.

---

## Contributing

If you encounter new issues, have improvements, or find clearer ways to explain these steps, please feel free to open an issue or submit a pull request! Your contributions help others.

---

## License

This guide is provided under the [MIT License](LICENSE).
