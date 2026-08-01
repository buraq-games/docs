# Managing users and squads

Everyone at Buraq has one account at [auth.buraq.games](https://auth.buraq.games),
which signs you into Plane, Mail, and the other internal tools.

## Viewing the directory

Members of the `admins` group (and squad leads, and anyone in
`directory-viewers`) can open the
[Authentik admin interface](https://auth.buraq.games/if/admin/) and go to
**Directory → Users** to see everyone, their email, and their groups. This
works with your normal account — no superuser needed.

## Squads, leads, and other groups

- **Squads** are the discipline groups: `engineering`, `art`, `design`,
  `production`, `qa`. Squad groups are children of `members`, so squad
  members automatically get app access (Plane, Mail).
- **Leads**: each squad has a `<squad>-leads` group (e.g.
  `engineering-leads`). Leads are automatically members of their squad,
  can view the directory, and can hot-fix their own squad's membership in
  the admin UI — but see the next section: git wins.
- **`cto`**: inherits directory viewing (via `admins`) and app access.
- **`directory-viewers`**: read-only directory access, no app access.

## Assigning someone to a squad (or any group)

Group membership is managed **in git, not in the UI**. The source of truth
is `authentik/blueprints/team-users.yaml` in the infra repo: add the group
name to the person's `groups:` list and deploy. Example:

```yaml
      groups:
        - !Find [authentik_core.group, [name, engineering]]
        - !Find [authentik_core.group, [name, engineering-leads]]
```

Every deploy re-applies this file, so membership changes made in the admin
UI are overwritten by the next deploy. (Leads' UI edits are a temporary
convenience only — make it permanent by editing team-users.yaml.)

## Who can do what

- **Admins / cto / directory-viewers / leads:** view all users and groups.
- **Leads only:** edit their own squad's membership in the UI (until the
  next deploy re-asserts git).
- **Nobody** (except break-glass `akadmin`): edit the `admins` group,
  grant superuser, or change Authentik configuration.

## Adding a brand-new person

Accounts are managed in the infra repo
(`authentik/blueprints/team-users.yaml`): add an entry with their
`groups:` (at minimum a squad, or `members` directly), deploy, then run
`scripts/onboard-user.sh <email>` to set their initial password.
