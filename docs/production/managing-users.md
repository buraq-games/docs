# Managing users and squads

Everyone at Buraq has one account at [auth.buraq.games](https://auth.buraq.games),
which signs you into Plane, Mail, and the other internal tools.

## Viewing the directory (admins)

Members of the `admins` group can open the
[Authentik admin interface](https://auth.buraq.games/if/admin/) and go to
**Directory → Users** to see everyone, their email, and their groups. This
works with your normal account — no superuser needed.

## Assigning someone to a squad

Squads are the discipline groups: `engineering`, `art`, `design`,
`production`, `qa`.

1. In the admin interface, go to **Directory → Groups** and open the squad.
2. On the **Users** tab, click **Add existing user** and pick the person.

That's it. Squad groups are children of `members`, so squad members
automatically get app access (Plane, Mail) — you don't need to add them to
`members` separately.

## What admins can and can't do

- **Can:** view all users and groups; add/remove people in the squad
  groups and `members`.
- **Can't:** edit the `admins` group, grant superuser, or change
  Authentik configuration. Those need the break-glass `akadmin` account.

## Adding a brand-new person

Accounts themselves are managed in the infra repo
(`authentik/blueprints/team-users.yaml`): add an entry, deploy, then run
`scripts/onboard-user.sh <email>` to set their initial password and add
them to `members`. Then assign their squad in the UI as above.
