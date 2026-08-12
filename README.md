# fleet-ops

Server-side sync layer for the ~/github fork fleet.

One scheduled workflow fast-forwards every fork's default branch from its
upstream daily via `gh repo sync`, so forks stay current even when every
local machine is off. Forks stay pristine mirrors: no workflow file is ever
committed to a fork (that would permanently diverge it and break the local
ff-only sync).

- `repos.txt` - the fork list, mirrored from `~/github/.fleet/manifest.yaml`
  (`sync: true` entries). Regenerate with:
  `awk '/^- name:/ {name=$3} /^  sync: true/ {print name}' ~/github/.fleet/manifest.yaml`
- `.github/workflows/fleet-sync.yml` - daily at 14:00 UTC + manual dispatch.
  Diverged forks are reported and skipped, never merged.
- Secret `FLEET_SYNC_TOKEN` (required): fine-grained PAT, Contents read-write
  on shreejitverma's repos. `github.token` cannot reach the other repos.

Local counterpart: `dotfiles-nix/files/bin/sync-forks` (daily launchd agent).
