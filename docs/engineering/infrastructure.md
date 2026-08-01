# Infrastructure

An overview of the platform running Buraq's internal tools — what's
deployed, how identity works, and where things live. This is written for
context, not as an ops runbook; the actual deploy scripts and configuration
live in the `infra` repo.

## What's running

Everything runs on a single VPS, deployed via GitHub Actions on every push
to `main` in `buraq-games/infra`. Five pieces:

| Service | What it's for |
| ------- | -------------- |
| **Traefik** | Reverse proxy in front of everything — TLS certs, routing `*.buraq.games` subdomains to the right container. |
| **Authentik** | Identity provider (IAM). One login for the team, used by every other tool below. |
| **Plane** | Project management — sprints, tasks, bugs. See [Using Plane](../production/using-plane.md). |
| **Mailcow** | Self-hosted mail server — `@buraq.games` mailboxes and webmail. |
| **Gitea** | Self-hosted git hosting + CI (engineering-only). See [Using Gitea](using-gitea.md). |

## Identity: one account per person

Authentik (`auth.buraq.games`) is the single source of truth for who's on
the team. Every account is `firstname.lastname@buraq.games` (a few
early accounts predate this convention). Two access levels:

- **`admins`** — infra-facing access (e.g. the Traefik dashboard), plus
  everything `members` can do.
- **`members`** — regular teammate access: currently Plane and Mailcow.
- **`engineering`** (a squad under `members`) — additionally gets Gitea,
  with `engineering-leads`/`admins` landing in Gitea's `Owners` team
  (org-admin) instead of the regular `engineers` team.

None of Plane, Mailcow, or Gitea have their own separate login — all three
are configured to authenticate exclusively through Authentik ("Continue
with Authentik" on Plane, "Single Sign-On" on Mailcow, "Sign in with
authentik" on Gitea). There's no native email/password login on any of
them — Gitea keeps a local break-glass `root` account for API/CLI recovery
if Authentik itself is ever down, but it has no web login form either.

New accounts are provisioned by an admin (declared in the `infra` repo,
not self-service signup). A generated one-time password is issued out of
band; the person is expected to change it after first login. There's
currently no self-service "forgot password" flow — a locked-out account
needs an admin to reset it manually.

## Plane specifics

Plane's Community edition has no native SSO, so it's wired up through a
workaround: Plane's self-hosted-GitLab login option is repurposed to talk
to Authentik instead of an actual GitLab instance (hence the login button,
cosmetically relabeled to say "Authentik"). Functionally it's normal OIDC
SSO — the GitLab framing is invisible other than that one button.

Known rough edge: a brand-new Plane account (no existing workspace
membership) gets dropped into a **new empty personal workspace** on first
login, rather than into the real `Buraq` workspace, even with a pending
invite on file. Until that's fixed, a new teammate's first login needs a
follow-up from an admin to move them into `Buraq` with the right role.

## Mail

Mailcow provisions a real mailbox automatically the first time someone
completes SSO login through it — no separate mailbox request needed, as
long as their Authentik account has a `@buraq.games` email.
