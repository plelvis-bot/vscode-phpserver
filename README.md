# PHP Server

Serve a project with PHP's built-in web server and expose a small JSON settings
API for remote agents.

## JSON settings API

The JSON API starts when the extension activates. By default it listens only on
`127.0.0.1:3888`; configure `phpserver.jsonApiHost` and `phpserver.jsonApiPort`
to change the bind address and port. To make it reachable through your own
public host or tunnel, bind to `0.0.0.0` and configure that host or tunnel to
forward the selected port.

The API has two operations:

- `GET /api/{key}` returns `{"key":"...","value":...}` or `404` if the key is
  not set.
- `PUT /api/{key}` stores the JSON request body as that key's value and returns
  the stored key and value.

Keys may contain letters, numbers, dots, underscores, and hyphens. Values can
be any valid JSON value up to 1 MB. Data is persisted in VS Code extension
global state, so it is available after the extension restarts and across
workspaces in the same VS Code profile.

Example:

```sh
curl -X PUT http://127.0.0.1:3888/api/agent-settings \
  -H 'Content-Type: application/json' \
  -d '{"model":"agent-v1","instructions":"Stay on task"}'

curl http://127.0.0.1:3888/api/agent-settings
```

Cross-origin browser requests are enabled. **The API has no authentication or
access control.** Anyone who can reach the bound address can read and overwrite
stored values. Do not expose it publicly unless your network or tunnel provides
appropriate access restrictions.
