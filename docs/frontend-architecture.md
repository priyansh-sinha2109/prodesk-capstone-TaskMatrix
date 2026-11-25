# Frontend architecture

Organize features by auth, workspace, projects, board, sprints, team and reports. The app shell provides navigation, workspace selector, focus target and error boundary. Shared controls include TaskCard, StatusMenu, FilterBar, EmptyState and ConfirmDialog. Route guards wait for session bootstrap, require current membership and hide unavailable actions. Services enforce the same policy.

| Slice | Properties | Actions | Selectors |
| --- | --- | --- | --- |
| authSlice | accessToken in memory, session phase | sessionReceived, signedOut | selectAuthenticated |
| workspaceSlice | currentWorkspaceId | workspaceSelected | selectWorkspaceId |
| boardSlice | projectId, filters, draggingTaskId | filtersChanged, dragStarted, dragEnded | selectBoardFilters |
| uiSlice | sidebarOpen, dialog, theme | toggleSidebar, closeDialog, themeChanged | selectTheme |
| notificationSlice | transient toast queue | enqueueToast, dismissToast | selectToasts |
| apiSlice | RTK Query cache and request state | generated endpoint actions, invalidateTags, resetApiState | generated endpoint selectors |

Server entities live only in RTK Query. Include workspace, project, page cursor and filter arguments in cache keys. Scope tags to workspace/project/entity. Task edits invalidate backlog, dashboard, workload and affected sprint lists. Patch only matching cached board views on move. Disable overlapping local moves, undo on failure, invalidate and refetch on conflict or uncertain network outcome. On switch cancel old subscriptions, reset transient selections and render only current-scope data. Logout clears all slices, subscriptions and API cache. Never persist JWTs, refresh tokens, passwords, invitation tokens or cached tenant data to localStorage.

Form drafts, focus and field errors remain local. Check required lengths and enum values on submit and map server errors to fields. Keep stale results visible during background refresh. A first load uses a skeleton, an empty result explains the next action, and failed reads offer retry. The [screen plan](ui-ux.md) maps routes and responsive behavior.
