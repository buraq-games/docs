# Using Plane

This is a beginner's guide to [Plane](https://github.com/makeplane/plane), the
tool we use to track sprints, tasks, and bugs. If you've never used a project
tracker before, start here — no prior experience assumed.

If you haven't read it yet, read [Milestones & Planning](milestones-and-planning.md)
first. This page shows you *where* our Agile process (sprints, backlog,
definition of done) lives inside Plane.

Plane lives at [plane.buraq.games](https://plane.buraq.games), signed into
with your Buraq account — see
[Accessing Buraq's Tools](../onboarding/accessing-buraq-tools.md) if you
haven't logged in yet. There's no separate invite step; an admin sets up
your account and you're in.

## What Plane is

Plane is an open-source project management tool, similar to Jira or Linear.
Everything we plan and track — sprint tasks, bugs, design work — lives there
instead of in chat messages or spreadsheets. If it's not in Plane, it doesn't
exist as far as the team's plan is concerned.

## Core concepts

Plane has a few building blocks. Here's how they map to our workflow:

| Plane term  | What it means here                                                    |
| ----------- | ----------------------------------------------------------------------|
| Workspace   | The whole studio — one workspace for Buraq Squad.                     |
| Project     | One game or initiative (e.g. SCARLET, HEXYBER).                       |
| Cycle       | A sprint. Ours are 2 weeks, matching [Milestones & Planning](milestones-and-planning.md). |
| Module      | A feature or system within a project (e.g. "Inventory", "Boss Fight 1"). |
| Issue       | A single task, bug, or piece of work — the smallest unit you interact with. |
| State       | Where an issue is in its lifecycle: Backlog, Todo, In Progress, In Review, Done. |
| View        | A saved filter, e.g. "My Issues" or "This Cycle's Bugs."              |

## Logging in and finding your project

1. Log in with the account your lead invited you with.
2. You'll land on your workspace. Pick your game's **project** from the
   sidebar.
3. Click **Cycles** in the left nav to see the current sprint.

## Your daily workflow

1. **Find your work.** Click **My Issues** (top nav or sidebar) to see
   everything assigned to you, or open the current **Cycle** to see the
   whole sprint board.
2. **Move issues as you work.** Drag an issue's card between states, or
   open it and change its **State** field:
   - `Todo` → not started yet
   - `In Progress` → you're actively working on it
   - `In Review` → done from your side, waiting on review/testing
   - `Done` → meets the [definition of done](milestones-and-planning.md#definition-of-done)
3. **Comment instead of DMing.** If you have a question or update about a
   task, post it on the issue, not in Discord/WhatsApp. That way the
   context stays attached to the work.
4. **Log blockers immediately.** If you're stuck, add a comment on the
   issue and flag it in standup — don't wait for the sprint review.

## Creating an issue

1. Click **+ New Issue** (top right, or `C` keyboard shortcut).
2. Fill in:
   - **Title** — short, specific ("Fix camera clipping in Level 3", not
     "camera bug").
   - **Description** — what's wrong / what's needed, and how to verify
     it's done. Link a design doc if one exists.
   - **Assignee** — who's doing the work.
   - **Cycle** — which sprint it belongs to (leave blank to add it to the
     backlog instead).
   - **Priority** and **Labels** — use these to mark bugs vs. features,
     and urgency.
3. Save. It now shows up in the relevant board and in the assignee's
   **My Issues**.

## Sprint planning in Plane

- At sprint planning, the lead opens the **Backlog** view, and the team
  drags prioritized issues into the new **Cycle**.
- During the sprint, new work that comes up goes into the **Backlog**,
  not straight into the active cycle — see
  [Scope changes](milestones-and-planning.md#scope-changes).
- At sprint review, walk the **Cycle** board: anything not in `Done`
  rolls into the next cycle.

## Tips for beginners

- You don't need to understand every Plane feature on day one — **My
  Issues** and moving cards between states covers 90% of daily use.
- If an issue is unclear, comment and ask rather than guessing.
- Keep issue titles short and states up to date — the whole point of the
  tracker is that anyone can glance at it and know the real status
  without asking.
