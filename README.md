# Home Assistant Quick Export Workflow

Quickly export Home Assistant configs via the Studio Code Server add-on — useful for sharing as context with AI models (Claude Code, ChatGPT, etc.).

## Prerequisites

- [Studio Code Server add-on](https://github.com/hassio-addons/addon-vscode) installed in Home Assistant

## One-Time Setup

Run this in the Studio Code Server terminal to create the export script:

```bash
echo 'cd /config && zip ha-configs.zip automations.yaml scripts.yaml scenes.yaml configuration.yaml .storage/lovelace* .storage/core.config_entries .storage/core.device_registry .storage/core.entity_registry .storage/core.area_registry' > /config/export.sh && chmod +x /config/export.sh
```

This creates `/config/export.sh` which zips your config files, dashboards, and registries into a single download.

## Usage

1. Open Studio Code Server in Home Assistant
2. Open the terminal (`Ctrl+backtick` or Terminal > New Terminal)
3. Run:
   ```bash
   /config/export.sh
   ```
4. In the file explorer (left sidebar), right-click `ha-configs.zip` > **Download**
5. Share the zip with your AI tool or drop it into your project

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

Edit `/config/export.sh` to add or remove files. For example, to also include helpers:

```bash
cd /config && zip ha-configs.zip \
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
