# openclaw-pull-notify-action

Reusable GitHub Action to send a lightweight "repo updated" notification to OpenClaw (or any webhook receiver), so your local agent can run a pull routine.

## Inputs

- `notify_url` (required): HTTPS endpoint receiving JSON payload
- `shared_secret` (optional): bearer token sent as `Authorization: Bearer ...`
- `event_name` (optional, default `repo.pull.request`)
- `extra_context` (optional)
- `timeout_seconds` (optional, default `10`)

## Output

- `delivered`: `true` when HTTP request succeeds

## Example usage in a repo

```yaml
name: Notify OpenClaw to Pull

on:
  push:
    branches: ["main"]

jobs:
  notify:
    runs-on: ubuntu-latest
    steps:
      - name: Notify OpenClaw
        uses: harvbot/openclaw-pull-notify-action@v1
        with:
          notify_url: ${{ secrets.OPENCLAW_NOTIFY_URL }}
          shared_secret: ${{ secrets.OPENCLAW_NOTIFY_TOKEN }}
          extra_context: "cfc-vault"
```

## Payload sent

```json
{
  "event": "repo.pull.request",
  "repo": "owner/repo",
  "branch": "main",
  "sha": "<commit_sha>",
  "actor": "username",
  "run_id": "123456789",
  "run_attempt": "1",
  "run_url": "https://github.com/owner/repo/actions/runs/123456789",
  "context": "optional"
}
```
