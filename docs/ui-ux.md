# Figma screen plan

Figma Design: Pending — public design link to be provided by the project owner.

| Screen and route | Layout | Interaction | API data |
| --- | --- | --- | --- |
| Authentication `/login, /register` | Email/password form, submit, invitation return path | Register or login and resume the intended route | Session and current user |
| Main dashboard `/w/:w` | Workspace selector, status totals, project list | Select a project or retry totals | Workspace, projects and dashboard counts |
| Projects `/w/:w/projects` | Project list, create/rename dialog, archive confirmation | Open project or confirm archive | Project list and detail |
| Kanban board `/w/:w/projects/:p/board` | Four status columns, cards and status menu | Drag or use keyboard menu, reconcile conflicts | Tasks and boardVersion |
| Sprint planning and backlog `/w/:w/projects/:p/planning` | Backlog list, planned sprint panel and completion dialog | Assign tasks, start or complete sprint | Backlog, tasks, sprints |
| Task details `/w/:w/projects/:p/tasks/:t` | Title, assignee, status, discussion | Save edits, assign member or post comment | Task, eligible members and comments |
| Analytics dashboard `/w/:w/projects/:p/analytics` | Date context, status totals and workload table | Inspect counts and switch project | Project analytics |
| Team management and settings `/w/:w/settings` | Membership table, role controls, invite dialog | Invite, change role or remove member | Workspace and memberships |

## Prototype and states

Link authentication to dashboard, project selection to board, board cards to task details, and project navigation to planning and analytics. Workspace navigation opens team settings. Build desktop and mobile frames. On mobile collapse navigation into a labeled drawer, stack summary panels, select one board column at a time and expand task details into a full page.

For each list frame design loading skeletons, a contextual empty action, a retryable error and populated success. Authentication shows pending submit, field errors and success redirect. A sprint completion preview shows how many unfinished tasks will return to backlog. On save success announce the result and restore focus. On stale edits retain the draft and offer reload. Include disabled Viewer controls or omit write actions. Label inputs and icon controls, pair color with status text, show focus rings, support Escape and keyboard board movement, and provide chart data in tables.
