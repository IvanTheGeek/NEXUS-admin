# Forgejo setup for Fun.Css fork

## Context

Goal: host a personal patched fork of slaveOftime/Fun.Css on Forgejo so we
can publish a patched `Fun.Css` NuGet package and consume it from
LauraExperiment. Mirrors the existing FnTools.DesignTokens setup pattern
but under the `IvanTheGeek` user, NOT the `FnTools` org.

Targets:
- Repo: forgejo.ivanthegeek.com/IvanTheGeek/Fun.Css
- NuGet feed: https://forgejo.ivanthegeek.com/api/packages/IvanTheGeek/nuget/index.json
- Package ID: Fun.Css (intentionally the same as upstream slaveOftime/Fun.Css
  — this is an override fork; our higher version supersedes upstream's
  in NuGet resolution order)
- First version to publish: 1.0.3-IvanTheGeek.1

Reference setup to mirror (do NOT touch): forgejo.ivanthegeek.com/FnTools/FnTools.DesignTokens

## Tasks

### 1. Create the repository `IvanTheGeek/Fun.Css`

Visibility: PUBLIC
Description: "Personal patched build of slaveOftime/Fun.Css with PR-equivalent fixes pending upstream merge"
Initialize: empty (do NOT auto-init README or anything — Claude session will push the initial content)

Via API (preferred):

curl -X POST
-H "Authorization: token $FORGEJO_TOKEN"
-H "Content-Type: application/json"
https://forgejo.ivanthegeek.com/api/v1/user/repos
-d '{"name":"Fun.Css","description":"Personal patched build of slaveOftime/Fun.Css","private":<true|false>,"auto_init":false}'

(Replace `<true|false>` per Ivan's visibility decision.)
### 2. Verify NuGet package registry works for IvanTheGeek user
Test that the feed URL responds with a NuGet service index JSON (not 404):
curl -s https://forgejo.ivanthegeek.com/api/packages/IvanTheGeek/nuget/index.json | head -20

Expected: JSON with `"version"` and `"resources"` keys. If 404, Forgejo
may need `[packages] ENABLED = true` in app.ini (it's the default — should
already work).
### 3. Token for publishing
Check whether existing `~/.config/forgejo-claude.token` has `write:package`
scope at user-level for IvanTheGeek (i.e., can write packages to
forgejo.ivanthegeek.com/api/packages/IvanTheGeek/...).
Test:
TOKEN=$(cat ~/.config/forgejo-claude.token)

Try a no-op pack list against IvanTheGeek's feed
curl -s -H "Authorization: token $TOKEN"
https://forgejo.ivanthegeek.com/api/packages/IvanTheGeek/nuget/v3/registration-base/Fun.Css/index.json
-o /dev/null -w "%{http_code}\n"

- If 404: token works, package just doesn't exist yet (expected). PROCEED.
- If 401/403: token lacks scope. Generate a new one via:
   - Forgejo Settings → Applications → Manage Access Tokens
   - Name: `fun-css-publish`
   - Scopes: `write:package` (read is implied)
   - Either replace the existing file or store at `~/.config/forgejo-claude-ivantg.token`
     (tell us which file the new token lives in so we can update publish.sh accordingly)
### 4. Add the token as a repo secret in IvanTheGeek/Fun.Css
Via API:
curl -X PUT
-H "Authorization: token $FORGEJO_TOKEN"
-H "Content-Type: application/json"
https://forgejo.ivanthegeek.com/api/v1/repos/IvanTheGeek/Fun.Css/actions/secrets/FORGEJO_NUGET_TOKEN
-d "{"data":"$NUGET_TOKEN"}"

Secret name MUST be `FORGEJO_NUGET_TOKEN` (matches the workflow YAML
we'll generate).
### 5. Verify Forgejo Actions / runners
Check whether the existing FnTools runner serves IvanTheGeek-owned repos
too, or whether a runner needs to be registered for the new repo.
Forgejo admin command (may need root):
forgejo actions list-runners --config /etc/forgejo/app.ini

OR via API:
curl -s -H "Authorization: token $FORGEJO_TOKEN"
https://forgejo.ivanthegeek.com/api/v1/admin/actions/runners

Look at the `labels` and `owner_id` fields on each runner. If no runner
is scoped to the IvanTheGeek user OR is site-level (unscoped), register
a new runner OR re-scope an existing one. For a typical Forgejo install,
site-level runners (no owner scope) serve every repo.
If a new runner is needed:
forgejo actions register-runner --instance https://forgejo.ivanthegeek.com
--token <REGISTRATION_TOKEN_FROM_ADMIN_UI>
--name "ivantg-runner" --labels "ubuntu-latest"

(`runs-on: ubuntu-latest` is what the workflow YAML will request.)
### 6. Report back
When all 5 steps are done, please confirm:
1. Repository created? URL: https://forgejo.ivanthegeek.com/IvanTheGeek/Fun.Css
2. Feed URL `https://forgejo.ivanthegeek.com/api/packages/IvanTheGeek/nuget/index.json` returns valid JSON?
3. Token works for IvanTheGeek package writes? Which file is it stored in locally?
4. `FORGEJO_NUGET_TOKEN` secret added to the new repo?
5. Runner available for `runs-on: ubuntu-latest` on the new repo?
Once that's confirmed, the Claude session that originally created this
list will push the initial branch (with the fork patches and `.forgejo/workflows/`)
to the repo and tag a release to trigger the publish.