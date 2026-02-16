# openclaw-pull-notify-action

Reusable GitHub Action to trigger OpenClaw webhooks on push events.

Primary use: send push metadata to OpenClaw `/hooks/wake` (or `/hooks/agent`) so your local agent can run a pull routine.

## Inputs

- `hook_url` (recommended): full webhook URL (e.g. `https://your-gateway/hooks/wake`)
- `notify_url` (legacy alias for `hook_url`)
- `shared_secret` (required): OpenClaw hooks token (sent as bearer auth)
- `mode` (optional, default `openclaw-wake`):
  - `openclaw-wake` → sends `{ text, mode }` for `/hooks/wake`
  - `openclaw-agent` → sends `{ message, name, wakeMode }` for `/hooks/agent`
  - `raw` → sends legacy JSON payload (`event/repo/branch/sha/...`)
- `wake_mode` (optional, default `now`): `now` or `next-heartbeat`
- `event_name` (optional, default `repo.pull.request`)
- `extra_context` (optional)
- `agent_name` (optional, default `GitHub`) for `openclaw-agent`
- `timeout_seconds` (optional, default `10`)

## Output

- `delivered`: `true` when HTTP request succeeds

## Example: OpenClaw wake hook (recommended)

```yaml
name: Notify OpenClaw to Pull

on:
  push:
    branches: ["main"]

jobs:
  notify:
    runs-on: ubuntu-latest
    steps:
      - name: Trigger OpenClaw wake hook
        uses: harvbot/openclaw-pull-notify-action@v2
        with:
          hook_url: ${{ secrets.OPENCLAW_HOOK_URL }} # e.g. https://host/hooks/wake
          shared_secret: ${{ secrets.OPENCLAW_HOOK_TOKEN }}
          mode: openclaw-wake
          wake_mode: now
          extra_context: cfc-vault
```

## Example: OpenClaw agent hook

```yaml
name: Notify OpenClaw Agent

on:
  push:
    branches: ["main"]

jobs:
  notify:
    runs-on: ubuntu-latest
    steps:
      - name: Trigger OpenClaw agent hook
        uses: harvbot/openclaw-pull-notify-action@v2
        with:
          hook_url: ${{ secrets.OPENCLAW_AGENT_HOOK_URL }} # e.g. https://host/hooks/agent
          shared_secret: ${{ secrets.OPENCLAW_HOOK_TOKEN }}
          mode: openclaw-agent
          wake_mode: now
          agent_name: GitHub
```

## Notes

- Keep your hook URL private and use a strong hook token.
- OpenClaw hooks docs: https://docs.openclaw.ai/automation/webhook
- `@v1` remains available for previous behavior; use `@v2` going forward.
