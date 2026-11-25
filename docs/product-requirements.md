# Product requirements and boundaries

| ID | Priority | User story and acceptance criterion |
| --- | --- | --- |
| FR-01 | P0 | As a new user I can register, sign in and sign out. A revoked session cannot refresh. |
| FR-02 | P0 | As a lead I can create a workspace. Its creation also creates my Owner membership. |
| FR-03 | P0 | As an Admin I can invite a colleague. Only the signed-in recipient with the matching email can redeem the invitation once. |
| FR-04 | P0 | As an Owner I can change a colleague’s role. The next protected request uses the new role. |
| FR-05 | P0 | As a lead I can create, rename and archive projects. Archived projects are read-only. |
| FR-06 | P0 | As a Member I can create, edit, assign and archive tasks. Assignment accepts a current workspace member with write access. |
| FR-07 | P0 | As a contributor I can move a task across board columns. Reload preserves its position and stale moves return a conflict. |
| FR-08 | P0 | As a planner I can see unarchived, unfinished tasks without a sprint in the project backlog. |
| FR-09 | P0 | As a lead I can plan, start and complete a sprint. Completion returns unfinished items to backlog and keeps finished items in sprint history. |
| FR-10 | P0 | As a Viewer I can see current project counts by status. Counts exclude archived tasks. |
| FR-11 | P1 | As a lead I can combine search, status and assignee filters and sort the resulting list. |
| FR-12 | P1 | As a Member I can post a comment and see it after reload. |
| FR-13 | P1 | As a lead I can trace a task change to an actor and timestamp. |
| FR-14 | P1 | As a planner I can see project status totals and open tasks per assignee including an unassigned bucket. |
| FR-15 | P1 | As an assignee I can see and acknowledge my own assignment notification. |
| FR-16 | P1 | As a keyboard or mobile user I can change task status through a menu and recover focus after closing a dialog. |
| FR-17 | P2 | As a lead I can inspect daily remaining work and compare completed work across sprints if historical snapshots are available. |
| FR-18 | P2 | As a collaborator I can see another user’s saved change without refreshing if live updates are enabled. |
| FR-19 | P2 | As a user I can select a dark theme that preserves readable contrast. |

| Action | Owner | Admin | Member | Viewer |
| --- | --- | --- | --- | --- |
| Read workspace, projects, boards and reports | Yes | Yes | Yes | Yes |
| Create, edit, assign, move or archive tasks and add comments | Yes | Yes | Yes | — |
| Manage projects and sprints, invite or manage Members and Viewers | Yes | Yes | — | — |
| Manage Admins and workspace settings | Yes | — | — | — |
| Read or acknowledge own notifications | Yes | Yes | Yes | Yes |

One Owner per workspace in the capstone. Ownership is fixed. Admins cannot change themselves, other Admins or the Owner. Membership removal clears assignments in that workspace.

## Domain rules

Every non-archived task appears in exactly one board status. Backlog membership depends on sprint assignment and completion state. Board movement changes status or position only. Attaching a task to a sprint is a separate edit. Completed sprints are frozen. Archived projects retain their history and reject child mutations. A task may transition between any two supported states in an editable project and sprint. Reopening completed sprint work requires creating a new backlog task.

Release evidence should include a clean workspace, a second workspace sharing one user, and a read-only stakeholder. Demonstrate one allowed and one denied action in each role. Product targets and submission gates are maintained in the [primary PRD](../README.md).
