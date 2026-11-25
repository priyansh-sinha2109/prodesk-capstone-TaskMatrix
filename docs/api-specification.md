# Mock REST contract

Base path `/api/v1`. All examples are fictional. Bearer authentication applies to signed-in operations. Every workspace route checks current membership and scoped parents. “Lead” means Owner or Admin, “Writer” adds Member, and “Reader” adds Viewer. Invitations require a matching authenticated email. Admins can only invite/manage Members and Viewers. Archived projects and completed sprint work reject edits. All request bodies are JSON. Unlisted parameters are unsupported. IDs are 24 lowercase hex characters. PATCH requires at least one editable field. Lists use opaque cursor pagination with a stable ID tie-breaker and limit 1–100 (default 25). The machine-readable [OpenAPI contract](openapi.yaml) provides individual schemas.

Errors use `{"error":{"code":"VERSION_CONFLICT","message":"Reload the board","requestId":"req-demo"}}`. 400 invalid input, 401 invalid session, 403 role or CSRF failure, 404 unavailable resource, 409 state conflict, 429 throttled. Creation returns 201, reads and updates 200, empty successes 204.

## Register

`POST /auth/register` · Public. 

Parameters: none. Body: email (required), password (required), displayName (required). Success: 201 `{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","email":"alex@example.test","displayName":"Alex"}`.

## Sign in

`POST /auth/login` · Public. Sets the rotated-session cookie. Passwords must fit 72 UTF-8 bytes.

Parameters: none. Body: email (required), password (required). Success: 200 `{"accessToken":"mock.jwt.value","csrfToken":"mock-csrf"}`.

## Refresh session

`POST /auth/refresh` · Session cookie. Require allowed Origin, rotate cookie and return new access and CSRF tokens.

Parameters: none. Body: none. Success: 200 `{"accessToken":"mock.jwt.value","csrfToken":"mock-csrf"}`.

## Sign out

`POST /auth/logout` · Session cookie. 

Parameters: none. Body: none. Success: 204 (empty).

## Current account

`GET /me` · Authenticated. 

Parameters: none. Body: none. Success: 200 `{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","email":"alex@example.test","displayName":"Alex"}`.

## My workspaces

`GET /workspaces` · Authenticated. 

Parameters: cursor (query), limit (query). Body: none. Success: 200 `{"items":[{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","name":"Studio"}],"nextCursor":null}`.

## Create workspace

`POST /workspaces` · Authenticated. 

Parameters: none. Body: name (required). Success: 201 `{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","name":"Studio"}`.

## Workspace detail

`GET /workspaces/{workspaceId}` · Reader. 

Parameters: workspaceId (path). Body: none. Success: 200 `{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","name":"Studio"}`.

## Rename workspace

`PATCH /workspaces/{workspaceId}` · Owner. 

Parameters: workspaceId (path). Body: name (required). Success: 200 `{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","name":"Studio"}`.

## Invite colleague

`POST /workspaces/{workspaceId}/invitations` · Lead. 

Parameters: workspaceId (path). Body: email (required), role (required). Success: 201 `{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","expiresAt":"2026-12-01T00:00:00Z","invitationToken":"fictional-once"}`.

## Accept invitation

`POST /invitations/accept` · Authenticated. Checks email, membership, token expiry and prior use.

Parameters: none. Body: token (required). Success: 200 `{"userId":"aaaaaaaaaaaaaaaaaaaaaaaa","role":"Member"}`.

## List members

`GET /workspaces/{workspaceId}/members` · Reader. 

Parameters: workspaceId (path), cursor (query), limit (query). Body: none. Success: 200 `{"items":[{"userId":"aaaaaaaaaaaaaaaaaaaaaaaa","role":"Member"}],"nextCursor":null}`.

## Change role

`PATCH /workspaces/{workspaceId}/members/{userId}` · Lead. 

Parameters: workspaceId (path), userId (path). Body: role (required). Success: 200 `{"userId":"aaaaaaaaaaaaaaaaaaaaaaaa","role":"Member"}`.

## Remove member

`DELETE /workspaces/{workspaceId}/members/{userId}` · Lead. 

Parameters: workspaceId (path), userId (path). Body: none. Success: 204 (empty).

## Project list

`GET /workspaces/{workspaceId}/projects` · Reader. 

Parameters: workspaceId (path), cursor (query), limit (query). Body: none. Success: 200 `{"items":[{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","name":"Website","boardVersion":2}],"nextCursor":null}`.

## Create project

`POST /workspaces/{workspaceId}/projects` · Lead. 

Parameters: workspaceId (path). Body: name (required). Success: 201 `{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","name":"Website","boardVersion":2}`.

## Project detail

`GET /workspaces/{workspaceId}/projects/{projectId}` · Reader. 

Parameters: workspaceId (path), projectId (path). Body: none. Success: 200 `{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","name":"Website","boardVersion":2}`.

## Rename project

`PATCH /workspaces/{workspaceId}/projects/{projectId}` · Lead. 

Parameters: workspaceId (path), projectId (path). Body: name (required). Success: 200 `{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","name":"Website","boardVersion":2}`.

## Archive project

`DELETE /workspaces/{workspaceId}/projects/{projectId}` · Lead. 

Parameters: workspaceId (path), projectId (path). Body: none. Success: 204 (empty).

## Board tasks

`GET /workspaces/{workspaceId}/projects/{projectId}/tasks` · Reader. 

Parameters: workspaceId (path), projectId (path), cursor (query), limit (query), q (query), status (query), assigneeId (query), sort (query). Body: none. Success: 200 `{"items":[{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","title":"Review navigation","status":"TODO","assigneeId":null,"sprintId":null,"position":0,"version":1}],"nextCursor":null,"boardVersion":2}`.

## Create task

`POST /workspaces/{workspaceId}/projects/{projectId}/tasks` · Writer. 

Parameters: workspaceId (path), projectId (path). Body: title (required), description, sprintId. Success: 201 `{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","title":"Review navigation","status":"TODO","assigneeId":null,"sprintId":null,"position":0,"version":1}`.

## Task detail

`GET /workspaces/{workspaceId}/projects/{projectId}/tasks/{taskId}` · Reader. 

Parameters: workspaceId (path), projectId (path), taskId (path). Body: none. Success: 200 `{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","title":"Review navigation","status":"TODO","assigneeId":null,"sprintId":null,"position":0,"version":1}`.

## Edit task

`PATCH /workspaces/{workspaceId}/projects/{projectId}/tasks/{taskId}` · Writer. 

Parameters: workspaceId (path), projectId (path), taskId (path). Body: title, description, sprintId, version (required). Success: 200 `{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","title":"Review navigation","status":"TODO","assigneeId":null,"sprintId":null,"position":0,"version":1}`.

## Assign task

`PUT /workspaces/{workspaceId}/projects/{projectId}/tasks/{taskId}/assignment` · Writer. 

Parameters: workspaceId (path), projectId (path), taskId (path). Body: assigneeId (required), version (required). Success: 200 `{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","title":"Review navigation","status":"TODO","assigneeId":null,"sprintId":null,"position":0,"version":1}`.

## Move task

`POST /workspaces/{workspaceId}/projects/{projectId}/tasks/{taskId}/move` · Writer. Null anchor appends. Anchor must belong to the same project and target column.

Parameters: workspaceId (path), projectId (path), taskId (path). Body: status (required), beforeTaskId (required), boardVersion (required), version (required). Success: 200 `{"task":{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","title":"Review navigation","status":"TODO","assigneeId":null,"sprintId":null,"position":0,"version":1},"boardVersion":3}`.

## Archive task

`DELETE /workspaces/{workspaceId}/projects/{projectId}/tasks/{taskId}` · Writer. 

Parameters: workspaceId (path), projectId (path), taskId (path). Body: none. Success: 204 (empty).

## Project backlog

`GET /workspaces/{workspaceId}/projects/{projectId}/backlog` · Reader. 

Parameters: workspaceId (path), projectId (path), cursor (query), limit (query). Body: none. Success: 200 `{"items":[{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","title":"Review navigation","status":"TODO","assigneeId":null,"sprintId":null,"position":0,"version":1}],"nextCursor":null}`.

## List sprints

`GET /workspaces/{workspaceId}/projects/{projectId}/sprints` · Reader. 

Parameters: workspaceId (path), projectId (path), cursor (query), limit (query). Body: none. Success: 200 `{"items":[{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","name":"Iteration one","status":"PLANNED"}],"nextCursor":null}`.

## Plan sprint

`POST /workspaces/{workspaceId}/projects/{projectId}/sprints` · Lead. 

Parameters: workspaceId (path), projectId (path). Body: name (required). Success: 201 `{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","name":"Iteration one","status":"PLANNED"}`.

## Rename planned sprint

`PATCH /workspaces/{workspaceId}/projects/{projectId}/sprints/{sprintId}` · Lead. 

Parameters: workspaceId (path), projectId (path), sprintId (path). Body: name (required). Success: 200 `{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","name":"Iteration one","status":"PLANNED"}`.

## Start sprint

`POST /workspaces/{workspaceId}/projects/{projectId}/sprints/{sprintId}/start` · Lead. 

Parameters: workspaceId (path), projectId (path), sprintId (path). Body: none. Success: 200 `{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","name":"Iteration one","status":"ACTIVE"}`.

## Complete sprint

`POST /workspaces/{workspaceId}/projects/{projectId}/sprints/{sprintId}/complete` · Lead. 

Parameters: workspaceId (path), projectId (path), sprintId (path). Body: none. Success: 200 `{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","name":"Iteration one","status":"COMPLETED","returnedToBacklog":2}`.

## Read discussion

`GET /workspaces/{workspaceId}/projects/{projectId}/tasks/{taskId}/comments` · Reader. 

Parameters: workspaceId (path), projectId (path), taskId (path), cursor (query), limit (query). Body: none. Success: 200 `{"items":[{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","text":"Ready for review","authorId":"aaaaaaaaaaaaaaaaaaaaaaaa"}],"nextCursor":null}`.

## Post comment

`POST /workspaces/{workspaceId}/projects/{projectId}/tasks/{taskId}/comments` · Writer. 

Parameters: workspaceId (path), projectId (path), taskId (path). Body: text (required). Success: 201 `{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","text":"Ready for review","authorId":"aaaaaaaaaaaaaaaaaaaaaaaa"}`.

## Project history

`GET /workspaces/{workspaceId}/projects/{projectId}/activity` · Reader. 

Parameters: workspaceId (path), projectId (path), cursor (query), limit (query). Body: none. Success: 200 `{"items":[{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","action":"task.moved","actorId":"aaaaaaaaaaaaaaaaaaaaaaaa"}],"nextCursor":null}`.

## My notifications

`GET /workspaces/{workspaceId}/notifications` · Reader. 

Parameters: workspaceId (path), cursor (query), limit (query). Body: none. Success: 200 `{"items":[{"id":"aaaaaaaaaaaaaaaaaaaaaaaa","taskId":"aaaaaaaaaaaaaaaaaaaaaaaa","readAt":null}],"nextCursor":null}`.

## Acknowledge own notification

`POST /workspaces/{workspaceId}/notifications/{notificationId}/read` · Reader. 

Parameters: workspaceId (path), notificationId (path). Body: none. Success: 204 (empty).

## Workspace dashboard

`GET /workspaces/{workspaceId}/dashboard` · Reader. 

Parameters: workspaceId (path). Body: none. Success: 200 `{"projects":[{"projectId":"aaaaaaaaaaaaaaaaaaaaaaaa","counts":{"TODO":1,"IN_PROGRESS":0,"IN_REVIEW":0,"DONE":0}}]}`.

## Project analytics

`GET /workspaces/{workspaceId}/projects/{projectId}/analytics` · Reader. 

Parameters: workspaceId (path), projectId (path). Body: none. Success: 200 `{"counts":{"TODO":1,"IN_PROGRESS":0,"IN_REVIEW":0,"DONE":0},"workload":[{"assigneeId":null,"open":1}]}`.
