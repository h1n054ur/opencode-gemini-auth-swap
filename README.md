# Gemini OAuth Plugin for Opencode (with Account Swap)

![License](https://img.shields.io/npm/l/opencode-gemini-auth-swap)

**Authenticate the Opencode CLI with your Google account + easily swap between multiple accounts.**

This is a fork of [opencode-gemini-auth](https://github.com/jenslys/opencode-gemini-auth) that adds multi-account profile management. Use your existing Gemini plan and quotas (including the free tier) directly within Opencode, and easily switch between different Google accounts/projects.

## Prerequisites

- [Opencode CLI](https://opencode.ai) installed.
- A Google account with access to Gemini.

## Installation

Add the plugin to your Opencode configuration file
(`~/.config/opencode/opencode.json` or similar):

```json
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": ["opencode-gemini-auth-swap@latest"]
}
```

Also install the CLI globally for account switching:

```bash
bun install -g opencode-gemini-auth-swap
```

## Usage

1. **Login**: Run the authentication command in your terminal:

   ```bash
   opencode auth login
   ```

2. **Select Provider**: Choose **Google** from the list.
3. **Authenticate**: Select **OAuth with Google (Gemini CLI)**.
   - A browser window will open for you to approve the access.
   - The plugin spins up a temporary local server to capture the callback.
   - If the local server fails (e.g., port in use or headless environment),
     you can manually paste the callback URL or just the authorization code.

Once authenticated, Opencode will use your Google account for Gemini requests.

## Account Switching

The main feature of this fork! Easily switch between multiple Google accounts.

### Save Current Account as Profile

After logging in with `opencode auth login`, save it as a named profile:

```bash
gemini-swap save work my-gcp-project-id
gemini-swap save personal another-project-id
```

### Switch Between Accounts

```bash
gemini-swap use work      # Switch to work account
gemini-swap use personal  # Switch to personal account
```

### Other Commands

```bash
gemini-swap list      # List all saved profiles
gemini-swap current   # Show current configuration
gemini-swap delete <name>  # Delete a profile
gemini-swap help      # Show help
```

### Workflow Example

```bash
# Login with first Google account
opencode auth login
# Select Google > OAuth with Google (Gemini CLI)

# Save as profile
gemini-swap save account1 project-id-for-account1

# Login with second Google account
opencode auth login

# Save as another profile
gemini-swap save account2 project-id-for-account2

# Now you can switch anytime:
gemini-swap use account1
opencode  # Uses account1

gemini-swap use account2
opencode  # Uses account2
```

## Configuration

### Google Cloud Project

By default, the plugin attempts to provision or find a suitable Google Cloud
project. To force a specific project, set the `projectId` in your configuration:

```json
{
  "provider": {
    "google": {
      "options": {
        "projectId": "your-specific-project-id"
      }
    }
  }
}
```

### Thinking Models

Configure "thinking" capabilities for Gemini models using the `thinkingConfig`
option in your `opencode.json`.

**Gemini 3 (Thinking Level)**
Use `thinkingLevel` (`"low"`, `"high"`) for Gemini 3 models.

```json
{
  "provider": {
    "google": {
      "models": {
        "gemini-3-pro-preview": {
          "options": {
            "thinkingConfig": {
              "thinkingLevel": "high",
              "includeThoughts": true
            }
          }
        }
      }
    }
  }
}
```

**Gemini 2.5 (Thinking Budget)**
Use `thinkingBudget` (token count) for Gemini 2.5 models.

```json
{
  "provider": {
    "google": {
      "models": {
        "gemini-2.5-flash": {
          "options": {
            "thinkingConfig": {
              "thinkingBudget": 8192,
              "includeThoughts": true
            }
          }
        }
      }
    }
  }
}
```

## Troubleshooting

### Manual Google Cloud Setup

If automatic provisioning fails, you may need to set up the project manually:

1. Go to the [Google Cloud Console](https://console.cloud.google.com/).
2. Create or select a project.
3. Enable the **Gemini for Google Cloud API**
   (`cloudaicompanion.googleapis.com`).
4. Configure the `projectId` in your Opencode config as shown above.

### Debugging

To view detailed logs of Gemini requests and responses, set the
`OPENCODE_GEMINI_DEBUG` environment variable:

```bash
OPENCODE_GEMINI_DEBUG=1 opencode
```

This will generate `gemini-debug-<timestamp>.log` files in your working
directory containing sanitized request/response details.

### Updating

Opencode does not automatically update plugins. To update to the latest version,
you must clear the cached plugin:

```bash
# Clear the specific plugin cache
rm -rf ~/.cache/opencode/node_modules/opencode-gemini-auth-swap

# Run Opencode to trigger a fresh install
opencode
```

## Development

To develop on this plugin locally:

1. **Clone**:

   ```bash
   git clone https://github.com/h1n054ur/opencode-gemini-auth-swap.git
   cd opencode-gemini-auth-swap
   bun install
   ```

2. **Run tests**:

   ```bash
   bun test
   ```

3. **Link**:
   Update your Opencode config to point to your local directory using a
   `file://` URL:

   ```json
   {
     "plugin": ["file:///absolute/path/to/opencode-gemini-auth-swap"]
   }
   ```

## Credits

This is a fork of [opencode-gemini-auth](https://github.com/jenslys/opencode-gemini-auth) by [jenslys](https://github.com/jenslys).

## License

MIT - see [LICENSE](LICENSE)
