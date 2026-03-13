# Home Assistant Quick Export Workflow

Quickly export Home Assistant configs via the Studio Code Server add-on — useful for sharing as context with AI models (Claude Code, ChatGPT, etc.).

## Prerequisites

- [Studio Code Server add-on](https://github.com/hassio-addons/addon-vscode) installed in Home Assistant

## One-Time Setup

Open the Studio Code Server terminal (`Ctrl+backtick` or Terminal > New Terminal) and run these two commands:

### 1. Show hidden files (including `.storage`)

By default, Studio Code Server hides `.storage` and other useful directories. Run this to make everything visible:

```bash
sed -i 's/"\*\*\/.storage": true/"**\/.storage": false/' /root/.local/share/code-server/User/settings.json 2>/dev/null || echo '{"files.exclude":{"**/.storage":false}}' > /root/.local/share/code-server/User/settings.json
```

> After running, `.storage` will appear in the file explorer sidebar. You may need to reload the window (`Ctrl+Shift+P` > "Developer: Reload Window") for it to take effect.

### 2. Create the export script

```bash
echo 'cd /config && zip -r ha-configs.zip automations.yaml scripts.yaml scenes.yaml configuration.yaml .storage/lovelace* .storage/core.config_entries .storage/core.device_registry .storage/core.entity_registry .storage/core.area_registry' > /config/export.sh && chmod +x /config/export.sh
```

This creates `/config/export.sh` which zips your config files, dashboards, and registries into a single download.

## Usage

1. Open Studio Code Server in Home Assistant
2. Open the terminal (`Ctrl+backtick` or Terminal > New Terminal)
3. Run:
   ```bash
   bash /config/export.sh
   ```
4. In the file explorer (left sidebar), right-click `ha-configs.zip` > **Download**
5. Share the zip with your AI tool or drop it into your project

## Troubleshooting

### Browser blocks the download

Brave and Chrome may block the download as "insecure" since Studio Code Server typically serves over HTTP (or self-signed HTTPS within your local network).

- **Brave**: Click the blocked download notification > "Keep dangerous file" (it's not actually dangerous)
- **Chrome**: Click the download bar warning > "Keep" or go to `chrome://downloads` and click "Keep dangerous file"
- **Firefox**: Usually downloads without issues

The file is safe — it's coming directly from your local Home Assistant instance on your own network.

### `.storage` folder not visible

If `.storage` still doesn't appear after running the setup command:

1. `Ctrl+Shift+P` > **"Preferences: Open User Settings (JSON)"**
2. Find `"files.exclude"` and set `"**/.storage"` to `false`
3. Save and reload the window

### Export script not found

Make sure you ran the setup command from step 2. Verify with:
```bash
cat /config/export.sh
```

## What Gets Exported

| File | Contents |
|------|----------|
| `automations.yaml` | All automations |
| `scripts.yaml` | All scripts |
| `scenes.yaml` | All scenes |
| `configuration.yaml` | Core HA configuration |
| `.storage/lovelace*` | All dashboard configurations |
| `.storage/core.device_registry` | Device names and ID mappings |
| `.storage/core.entity_registry` | Entity customizations |
| `.storage/core.area_registry` | Room and area definitions |
| `.storage/core.config_entries` | Integration setup |

## Customizing

Edit `/config/export.sh` to add or remove files. For example, to also include restore state:

```bash
cd /config && zip -r ha-configs.zip \
  automations.yaml \
  scripts.yaml \
  scenes.yaml \
  configuration.yaml \
  .storage/lovelace* \
  .storage/core.config_entries \
  .storage/core.device_registry \
  .storage/core.entity_registry \
  .storage/core.area_registry \
  .storage/core.restore_state
```

> **Warning:** Be careful exporting files that may contain passwords/tokens (like `secrets.yaml`). Only share with tools and services you trust. The `.storage/core.config_entries` file may contain API keys for integrations.

## Why This Exists

When working with AI coding assistants on Home Assistant projects, they need your current config files as context. This workflow makes it a 10-second process: run script, download zip, share.