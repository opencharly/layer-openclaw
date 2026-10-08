# openclaw

The **OpenClaw family** of OpenCharly — the AI-gateway image and its layer, owned
here as one repo instead of spread across the CachyOS image family.

| What | Name | Kind |
|---|---|---|
| Gateway image | `openclaw` | box (base `cachyos`) |
| Gateway layer | `openclaw` | candy (`pod-openclaw`'s successor) |
| R10 witness | `check-openclaw-pod` | disposable pod bed |

OpenClaw itself is a multi-channel AI gateway: it connects LLM agents to
messaging channels and exposes them through a WebSocket API, a CLI and a web
Control UI. This repo ships the gateway as a headless service on port `18789` —
no desktop, no browser — plus the disposable bed that proves it live.

## Quick start

```bash
charly box build openclaw
charly config openclaw
charly start openclaw
# gateway at http://localhost:18789
charly check run check-openclaw-pod      # the disposable R10 witness
```

The gateway binds loopback and a socat relay forwards it onto the container
interface, so the Control UI never sees a non-loopback origin. A `data` volume
persists `~/.openclaw`. The gateway's own `/healthz` and `/readyz` endpoints are
the liveness and readiness probes, and the `openclaw:` check verb
(`opencharly/plugin-openclaw`) reaches them from the charly host.

## Layout

- `charly.yml` — the project manifest: the `cachyos` namespace import, `defaults`,
  the `discover:` tree and the inline `check-openclaw-pod` bed.
- `box/openclaw/charly.yml` — the gateway image and its `skill:` entity.
- `candy/openclaw/charly.yml` + `package.json` + `openclaw.service` — the gateway
  layer (the npm pin, the service it runs, and a host systemd unit reference) and
  its `skill:` entity.
- `.github/workflows/tag-on-merge.yml` — the org tag-on-merge caller.
- `AGENTS.md` — the repo's agent guidance; `README.md` — this overview.

## Ownership

This repo owns the family: its image, its layer, its bed and its skills. The
`openclaw:` **check verb** is a separate concern and lives in its own repo,
[`opencharly/plugin-openclaw`](https://github.com/opencharly/plugin-openclaw);
the image composes that plugin candy so the bed can author `openclaw:` steps.

Related:

- `/charly-openclaw:openclaw` — the owning skill for the image.
- `/charly-automation:openclaw-deploy` — gateway configuration, model auth and
  channel setup.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI
  and image builder.
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the
  umbrella and the authoritative rulebook.
