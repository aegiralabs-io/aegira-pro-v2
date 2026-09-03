# Aegira

Aegira is a Rust-based Linux incident detection and automated recovery engine.

## Current engine

- Monitors `/var/log/aegira/system.log`
- Processes `[ERROR]` and `[CRITICAL]` entries
- Matches incidents against built-in and custom JSON rules
- Supports service and Docker-container remediation
- Verifies recovery with retries
- Handles log rotation/truncation
- Hot-reloads changed rule files
- Prevents Aegira from restarting itself
- Supports cooldown/deduplication
- Provides CLI status, rule listing, history, and configuration

## Pro-ready automation

Custom rules support these action policies:

- `auto_recover` - execute remediation and verify it
- `alert_only` - notify without remediation
- `dry_run` - report what would be done without executing it
- `approval_required` - notify that remediation requires approval; no automatic execution

An explicit `alert_only` remediation type is also supported for simple alert rules.

## Gmail alerts through Composio

Aegira uses Composio's `GMAIL_SEND_EMAIL` tool for Gmail notifications.

The binary does **not** contain a Composio API key. Configure the key through the deployment environment. For testing, set:

- `COMPOSIO_API_KEY`
- `COMPOSIO_USER_ID` (or `alerts.composio_user_id` in `/etc/aegira/config.json`)
- `AEGIRA_ALERT_EMAIL` (or `alerts.recipient_email` in `/etc/aegira/config.json`)

Then enable alerts:

```bash
sudo ./target/release/aegira configure alerts on your@email.example
```

The Composio Gmail connection must already be configured for the Composio user/account. By default, Aegira alerts on unknown incidents, alert-only rules, and recovery failures. Successful recoveries are logged locally but do not send email unless `alerts.notify_on_recovery` is enabled. See the Composio Gmail documentation for OAuth and `GMAIL_SEND_EMAIL` setup.

## CLI

```text
aegira install
aegira status
aegira show-rules
aegira history
aegira configure service <name>
aegira configure container <name>
aegira configure alerts <on|off> [recipient_email]
aegira license
aegira run
```

## Development license mode

License enforcement is intentionally disabled in this development/testing build. The code contains the production hook where server-side subscription validation will be enabled later.

A production subscription should be managed by a billing backend. The local binary should validate a license/subscription status rather than process recurring card payments itself. The billing system can renew the subscription monthly until cancellation and issue/revoke the associated license entitlement.

Do not place real license keys, payment credentials, or Composio API keys in Git.

## Distribution / source protection

This repository is intended for controlled/proprietary development. Do not publish the Pro source repository publicly if the goal is to keep the Pro implementation private.

For a public GitHub presence, keep the Free source in a public repository and keep Pro source in a private repository. Distribute the Pro product as a compiled, signed release binary or installer. A public repository without an open-source license does not grant users permission to reuse the code, but public source is still visible and can be copied; source secrecy therefore requires repository access control, not copyright notices alone.

## Build

```bash
cargo build --release
```

## Install

```bash
sudo ./target/release/aegira install
sudo ./target/release/aegira configure service cron
```

## Example custom alert-only rule

```json
[
  {
    "id": "custom_alert_test",
    "name": "Custom Alert Test",
    "severity": "high",
    "error_patterns": ["AEGIRA_CUSTOM_TEST"],
    "context_patterns": [],
    "remediation": {
      "type": "alert_only"
    },
    "verification": {
      "type": "none"
    },
    "action": "alert_only",
    "priority": 100
  }
]
```

Custom rules are loaded from `/etc/aegira/rules/custom/` and are hot-reloaded when their files change.

The installer creates `/etc/aegira/composio.env` with restrictive permissions and the systemd service loads it automatically. Put the Composio project API key there as:

```text
COMPOSIO_API_KEY=your_key_here
COMPOSIO_USER_ID=your_composio_user_id
```

Do not commit this file.
