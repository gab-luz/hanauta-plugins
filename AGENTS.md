# AGENTS.md

## Purpose

This repository (`/home/gabi/dev/hanauta-plugins`) is the plugin catalog/metadata source for Hanauta Marketplace.

AI agents updating plugin code or metadata must follow these rules.

## Source of Truth

- Plugin runtime files should live in each plugin repository (for example `/home/gabi/dev/hanauta-plugin-vpn-control`).
- `hanauta-plugins/plugins.json` should reference released plugin entries and versions.
- Do not edit plugin runtime code directly inside cached runtime folders unless doing emergency hotfix sync.

## Root Access Policy

- Plugins must not request ad-hoc Polkit prompts during normal popup interaction.
- Privileged operations should go through Hanauta root service pathways.
- If a plugin needs root capabilities, wire that via:
  - `hanauta-install.json` privileged install/uninstall hooks.
  - A root service/agent installed once.
  - Non-interactive request/response from plugin UI to that service.

## Installation Expectations

- Marketplace install should be sufficient to make a plugin functional.
- If plugin needs additional root runtime dependencies (for example `resolvconf`), install them during privileged install.
- Avoid requiring users to run extra manual commands after Marketplace install.

## Hanauta API Integration

- Plugin repositories should expose a `hanauta_plugin.py` with `register_hanauta_plugin()` so Hanauta can discover plugin UI/services through the host API.
- Launch plugin entry scripts through API helpers (for example `entry_command` and `run_bg`) instead of hardcoded shell commands.
- Runtime paths must avoid hardcoded root-owned locations like `/hanauta/...`; prefer plugin API context, `HANAUTA_ROOT`, or user-writable fallbacks under `~/.local/share/hanauta`.
- If a plugin has standalone scripts (example: wallpaper manager), keep the launcher and service-section wiring in `hanauta_plugin.py` inside the plugin repo.

## Update Workflow

1. Change plugin repo code in `/home/gabi/dev/hanauta-plugin-*`.
2. Run syntax checks (`python3 -m py_compile` or equivalent).
3. Commit and push plugin repo.
4. Update marketplace metadata in `plugins.json` only when needed.
5. Validate runtime resolves updated plugin paths.

## Runtime Verification Checklist

- Confirm active plugin source path at runtime.
- Confirm required root service unit is active.
- Confirm plugin cache/state files are being written in expected user path.
- Confirm popup controls exist and actions produce visible state changes.

## Safety

- Never use destructive git commands (`reset --hard`, checkout revert) unless explicitly requested.
- Preserve unrelated local changes.
