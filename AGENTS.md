# AGENTS.md — openclaw

The **OpenClaw family** repo. It owns the gateway layer candy
(`candy/openclaw`, the successor to `opencharly/pod-openclaw`), the gateway image
(`box/openclaw`), the disposable R10 bed (`check-openclaw-pod`) and both skill
entities. The `openclaw:` **check verb** is a separate concern and lives in its own
repo, `opencharly/plugin-openclaw`; the image composes that plugin candy so this
repo's bed can author `openclaw:` steps.

It is a **family repo**, not a distro repo: it imports the `cachyos` namespace for
its base and builder (one-directional — distro-cachyos imports nothing back) and
keeps the family's candy, image, bed and skills in one place.

## Canonical files

- `charly.yml` — the project manifest: the `cachyos` import, `defaults` (incl. the
  `builder:` map for the arch npm/pixi builders inside that namespace), the
  `discover:` tree, and the inline `check-openclaw-pod` bed.
- `box/openclaw/charly.yml` — the gateway image plus its `skill:` entity.
- `candy/openclaw/charly.yml` + `package.json` + `openclaw.service` — the gateway
  layer (the npm pin, the service it runs, and the host systemd unit reference)
  plus its `skill:` entity.
- `.github/workflows/tag-on-merge.yml` — the org tag-on-merge caller (CalVer tag +
  `CHANGELOG/` on merge). Present from the repo's first commit on purpose: a repo
  without it merges but never gets a tag.
- `CHANGELOG/` — per-CalVer history, written at merge time.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-openclaw:openclaw` — the owning skill: the headless gateway image, its
  port/relay architecture and verification.
- `/charly-openclaw:openclaw-layer` — the layer's own reference (npm pin, engine
  floor, service, volume, relay).
- `/charly-automation:openclaw-deploy` — gateway configuration, model auth and
  channel setup.
- `/charly-image:image`, `/charly-image:layer` — composition and candy authoring.
- `/charly-check:check` — the disposable beds and `plan:` authoring.
- `/charly-internals:git-workflow` — before any git/PR action.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest, the
  box, the candy and the two skill entities.
- `charly box generate` — the emission path: it must produce the box's
  Containerfile plus the `cachyos.cachyos`, `cachyos.arch.arch` and
  `cachyos.arch.arch-builder` chain.
- `charly check run check-openclaw-pod` — the R10 witness: a fresh rebuild of the
  gateway image on the CachyOS base, a disposable pod deploy, the baked gateway
  checks and the plugin's `openclaw:` verb steps.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo gate beyond the caller workflow above.

## Modify this repo

- Edit the box manifest and its `skill:` entity together, and the candy manifest
  and its `skill:` entity together — the skill is the projected usage source, so a
  change not mirrored in its skill leaves the marketplace corpus stale.
- The npm pin lives in `package.json` and is asserted in the candy's plan; the
  engine-range check asserts the runtime the package requires. Keep the three in
  step when the pin moves.
- The gateway binds loopback and socat relays it (`port_relay: 18789`); the
  `port:` field, the `port_relay:` field and the service exec stay in step.
- The `data` volume at `~/.openclaw` is the persistent store (the gateway's own
  config, agents and sessions live there); keep the service exec and the volume
  path in step.
- **Do not move the `cachyos` import back to a tag older than `v2026.281.1001`.**
  The tags before it pin `opencharly/pod-openclaw` in the CachyOS refs list, whose
  candy is also named `openclaw`; the resolver fails closed on that collision with
  `local candy "openclaw" shadows remote candy "github.com/opencharly/pod-openclaw"`.
- The family ships the gateway **only**. Do not add desktop, browser or tool
  bundles to this image and do not create `<family>-full` / `<family>-ml` variant
  metalayers: a consumer that wants more composes the candies it needs beside this
  one.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge on
  PASS. Direct pushes to `main` are blocked, and `main` itself is created by the
  org's `bootstrap-repo-main` workflow (the ruleset's `creation` rule denies every
  operator path).
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the PR
  body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
