# Using Gitea

[Gitea](https://about.gitea.com/) is our self-hosted git host and CI runner,
at [git.buraq.games](https://git.buraq.games). It's engineering-only (art,
design, and production don't have access) and everything about who can do
what there — orgs, teams, repo access — is driven entirely from git, not
clicked together in the UI. If you're new to self-hosted git or Gitea
specifically, this page covers day-to-day use; see
[Infrastructure](infrastructure.md) for how the service itself is deployed.

## Logging in

Click the **Gitea** tile on [auth.buraq.games](https://auth.buraq.games) (see
[Accessing Buraq's Tools](../onboarding/accessing-buraq-tools.md)), or go
straight to `https://git.buraq.games` and click **Sign in with authentik**.

There is **no username/password login form** — Gitea is SSO-only. Your
account is created automatically the first time you log in through
Authentik, and it's already a member of the `buraq` organization with the
right team based on your squad (see [Org membership](#org-membership)
below). There's no invite step to wait on.

## Cloning and pushing

Both HTTPS and SSH work, SSH on the normal port 22:

```bash
# HTTPS
git clone https://git.buraq.games/buraq/some-repo.git

# SSH — no port suffix needed, exactly like GitHub
git clone git@git.buraq.games:buraq/some-repo.git
```

### Adding your SSH key

1. Generate a key pair if you don't have one:
   ```bash
   ssh-keygen -t ed25519 -C "your@email.com"
   ```

2. Copy the public key:
   ```bash
   pbcopy < ~/.ssh/id_ed25519.pub
   ```

3. In the Gitea web UI, go to **Settings → SSH / GPG Keys → Add Key**, paste the public key, and save.

4. Verify the key is registered by signing a test challenge:
   ```bash
   echo -n 'gitea' | ssh-keygen -Y sign -n gitea -f ~/.ssh/id_ed25519
   ```
   This signs the string `gitea` with your key using the SSH signature format Gitea expects. If the key is registered correctly, Gitea can verify it against the public key on file.

5. Test the SSH connection:
   ```bash
   ssh -T git@git.buraq.games
   ```

For HTTPS pushes (and any tool that wants git-over-HTTP auth), use a
**personal access token** instead of a password:

**Settings → Applications → Generate New Token**, then use the token as
your password when git prompts for one (or embed it: `https://<token>@git.buraq.games/buraq/some-repo.git`).

!!! note "Why a token, not a password?"
    Password login is disabled instance-wide (SSO-only), so there's no
    account password to type here even if you wanted to. A personal access
    token is the equivalent for anything that isn't a browser — git-over-HTTP,
    the API, or the `tea` CLI below.

## Org membership

Everyone's Gitea org/team membership is computed from
`authentik/blueprints/team-users.yaml` in the infra repo, the same file that
drives your squad in Authentik. Squad membership maps to a Gitea team, and
every deploy re-asserts it — so if you're added to `engineering` in that
file, you land in the `engineers` team on the next deploy with no separate
Gitea-side step. This also means membership changes made by hand in the
Gitea UI don't stick — the next deploy reverts them. If your access looks
wrong, the fix is in `team-users.yaml`, not the Gitea UI. See
[Managing users and squads](../production/managing-users.md) for how that
file works.

One consequence: regular `engineers` team members can push to any repo in
the org but can't **create** new repos or org-level settings — that needs
`Owners` (squad leads and admins). Ask a lead if you need a new repo.

## Repo naming conventions

Repositories follow a prefix convention to make the org's purpose clear at a glance:

| Prefix       | Purpose              | Example                        |
|--------------|----------------------|--------------------------------|
| `game-*`     | Game projects        | `game-wave-survivor`           |
| `docs`       | Documentation        | `docs`                         |
| `infra`      | Infrastructure       | `infra`                        |
| `tool-*`     | Shared tools         | `tool-rust-bridge`             |
| `meta-*`     | Meta/org-wide        | `meta-handbook`                |

Use `tea repo create --name game-<name>` when creating game repos.

## The `tea` CLI

[`tea`](https://gitea.com/gitea/tea) is Gitea's official CLI client — the
equivalent of GitHub's `gh`. It's not something the infra provisions for
you; install it yourself if you want it:

```bash
# macOS
brew install tea

# or download a binary from https://gitea.com/gitea/tea/releases
```

Log in once with a personal access token (see above):

```bash
tea login add --url https://git.buraq.games --token <your-token>
```

Then use it like `gh`:

```bash
tea issues                              # list issues in the current repo
tea pulls create --title "..." --base main
tea releases
```

## CI with Gitea Actions

CI is Gitea Actions — syntax-compatible with GitHub Actions. Add workflows
under `.gitea/workflows/` in your repo exactly like you would
`.github/workflows/` on GitHub:

```yaml
# .gitea/workflows/ci.yml
on: [push]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: echo "hello from Gitea Actions"
```

One shared runner (`act_runner`) picks up jobs from every repo in the
instance — no per-repo runner setup needed. Check the **Actions** tab on
your repo to see run status and logs.

## Notifications

Issue/PR/mention/review emails are on by default, sent to your
`@buraq.games` address. If you'd rather not get them, mute a specific repo
(bell icon on the repo page) or adjust the global setting under **Settings
→ Notifications**.
