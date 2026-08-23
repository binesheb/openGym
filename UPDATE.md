# Updating openGym

This fork follows the repository's `main` branch. Updates are **manual and opt-in**: the running service never rewrites itself or silently pulls new code.

## Before updating

Back up the `data/` directory. It contains profiles, passkey records, workout history, settings, and the session secret.

```bash
cp -a data "data.backup-$(date +%Y%m%d-%H%M%S)"
```

## Update a source checkout

From the repository directory:

```bash
git status --short
git fetch origin
git pull --ff-only origin main
docker compose pull
docker compose up -d
```

If you build images locally instead of using the published images, replace the last two commands with:

```bash
docker compose up -d --build
```

`--ff-only` prevents Git from creating an unexpected merge commit during an operational update.

## Verify

Open the configured `WEB_PORT` (default `http://localhost:8080`) and confirm that sign-in and a normal workout screen load. If a previous release is being upgraded, also check the repository changelog for any release-specific migration notes.

## Roll back

If the update causes a problem, return to the previously known-good Git revision and rebuild/restart the services:

```bash
git log --oneline -5
git reset --hard <known-good-commit>
docker compose up -d --build
```

Do not delete `data/` as part of a code rollback. Restore the backup only if the data itself was intentionally migrated and the repository's release notes instruct you to do so.

## Automatic updates

Automatic source updates are intentionally **not enabled**. This deployment handles authentication and persistent user data, so unattended changes to application code or container images can create an availability or compatibility risk. Use the documented manual update path, preferably after reviewing the changelog and testing a backup/rollback procedure.
