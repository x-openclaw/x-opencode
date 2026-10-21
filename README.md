# x-opencode

OpenClaw provider plugin: OpenCode Go models through an AxonHub gateway (OpenAI format).

- Provider `x-opencode/`, shown in the model picker as **X Opencode**.
- Models: models with `owned_by: opencode_go`, discovered live from AxonHub's `/v1/models`. New models appear after a Refresh in the model picker, or once the 60 s discovery cache expires.
- Embedding and other non-chat models are filtered out.
- Adding a key never replaces the primary model chosen in the UI.

## API address and key

Set them in the OpenClaw UI (**Plugins → X Opencode → Settings**) or with env vars. The UI setting wins.

| Setting | UI field | Env var | Example |
| ------- | -------- | ------- | ------- |
| API address | `baseUrl` | `X_OPENCODE_BASE_URL` | `https://axonhub.example.com/v1` |
| API key | `apiKey` | `X_OPENCODE_API_KEY` | API key |
| Context window | `contextWindow` | `X_OPENCODE_CONTEXT_WINDOW` | optional, default: the value AxonHub reports |
| Streaming | `streaming` | `X_OPENCODE_STREAMING` | `true` (default) |

The key can also be saved as an auth profile under **Settings → Model providers**, or with `openclaw models auth paste-api-key --provider x-opencode`. Order: UI `apiKey`, then the auth profile, then the env var.

With Docker Compose, the container only sees variables passed under `environment:`, e.g. `- X_OPENCODE_API_KEY=${X_OPENCODE_API_KEY}`.

## Install into an OpenClaw container

The plugin lives on the data volume, so it survives redeploys.

```sh
C=<openclaw container>
docker exec $C rm -rf /data/plugins/x-opencode
docker cp . $C:/data/plugins/x-opencode
# OpenClaw ignores plugin dirs that are not root-owned or are group-writable
docker exec $C sh -c 'rm -rf /data/plugins/x-opencode/.git && chown -R root:root /data/plugins/x-opencode && chmod 755 /data/plugins/x-opencode && chmod 644 /data/plugins/x-opencode/*'
docker exec $C openclaw plugins install --link /data/plugins/x-opencode --force
```

## Update after editing

Run the first three commands above again, then:

```sh
docker exec $C openclaw plugins reload x-opencode
```
