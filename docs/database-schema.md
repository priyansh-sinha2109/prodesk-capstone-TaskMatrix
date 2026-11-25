# MongoDB schema proposal

Every collection has a generated ObjectId `_id` and required BSON Date `createdAt` and `updatedAt` set by the server. “!” is required, “?” defaults to null. Fields without a stated default need explicit input or service-derived values. Other than _id and the unique indexes listed, no uniqueness is assumed.

## users

Account identity. Fields: email:String! normalized unique, passwordHash:String! bcrypt, displayName:String! 1–80.

Indexes: unique(email).

Global identity. Disable login and revoke sessions on deactivation.

## workspaces

Tenant boundary. Fields: name:String! 1–100, createdBy:ObjectId! → users.

Indexes: (createdBy,createdAt).

List through memberships. Retain workspace in capstone.

## memberships

Workspace access. Fields: workspaceId:ObjectId! → workspaces, userId:ObjectId! → users, role:String! Owner/Admin/Member/Viewer.

Indexes: unique(workspaceId,userId), unique(workspaceId) where role=Owner.

Remove transactionally with assignment cleanup. Check live membership on reads and writes.

## invitations

One-time admission. Fields: workspaceId:ObjectId! → workspaces, email:String! normalized, role:String! Admin/Member/Viewer, tokenHash:String! unique, expiresAt:Date! +24h, acceptedAt:Date?.

Indexes: unique(tokenHash), (workspaceId,email), TTL(expiresAt).

Check expiry explicitly. Bind redemption to account email and tenant.

## projects

Work container. Fields: workspaceId:ObjectId! → workspaces, name:String! 1–100, boardVersion:Int32! 0, archivedAt:Date?.

Indexes: (workspaceId,archivedAt,name).

Archive in place. Scoped parent lookup precedes every child query.

## tasks

Board and backlog item. Fields: workspaceId:ObjectId! → workspaces, projectId:ObjectId! → projects, sprintId:ObjectId? → sprints, assigneeId:ObjectId? → users, title:String! 1–200, description:String! empty, status:String! TODO, position:Int32! append, version:Int32! 0, archivedAt:Date?.

Indexes: (workspaceId,projectId,status,position,_id), (workspaceId,projectId,sprintId), (workspaceId,assigneeId).

Archive in place. Validate assignee membership and project/sprint parent.

## sprints

Planning interval. Fields: workspaceId:ObjectId! → workspaces, projectId:ObjectId! → projects, name:String! 1–100, status:String! PLANNED, startedAt:Date?, completedAt:Date?.

Indexes: (workspaceId,projectId,createdAt), unique(workspaceId,projectId) where status=ACTIVE.

Retain completed sprints. Parent scope is immutable.

## comments

Task discussion. Fields: workspaceId:ObjectId! → workspaces, projectId:ObjectId! → projects, taskId:ObjectId! → tasks, authorId:ObjectId! → users, text:String! 1–2000.

Indexes: (workspaceId,projectId,taskId,createdAt).

Retain under archived task. Require ancestor visibility.

## activity_logs

Change history. Fields: workspaceId:ObjectId! → workspaces, projectId:ObjectId! → projects, actorId:ObjectId! → users, action:String! allowlisted, entityId:ObjectId! target.

Indexes: (workspaceId,projectId,createdAt).

Append only. Target type determined by action. Scope to project.

## notifications

Assignment inbox. Fields: workspaceId:ObjectId! → workspaces, userId:ObjectId! → users, taskId:ObjectId! → tasks, readAt:Date?.

Indexes: (workspaceId,userId,createdAt).

Recipient-only reads and updates. Suppress when membership ends.

## refresh_sessions

Rotating login credential. Fields: userId:ObjectId! → users, familyId:ObjectId! generated, tokenHash:String! unique, expiresAt:Date! +7d, revokedAt:Date?.

Indexes: unique(tokenHash), (userId,familyId), TTL(expiresAt).

Account scoped. Retain revoked hash until expiry for reuse detection.

## Relationships and atomic changes

Each reference points to one parent. Parents may have zero to many children. Optional references permit zero parents. Memberships join users and workspaces many-to-many. Tasks and sprints share an immutable workspace/project pair. A sprint holds many tasks, each assigned to at most one sprint.

Statuses are TODO, IN_PROGRESS, IN_REVIEW and DONE. A project-wide integer position sequence per status includes all active tasks across sprints. A move supplies a same-column anchor or appends, checks project boardVersion and task version, resequences affected columns and increments versions in a transaction. All changes to board membership or order advance boardVersion. Read order is position then _id. A stale write returns 409 and a refreshed board.

Sprint transitions are PLANNED → ACTIVE → COMPLETED. Completion atomically freezes DONE tasks, detaches unfinished tasks without changing status, records completion time and appends history. Serialize task changes and completion with a shared project version write. Only one ACTIVE sprint is allowed per project. Use transactions for workspace plus Owner membership, invitation consumption plus membership, role downgrade/removal plus assignment cleanup, refresh rotation, and domain mutations with associated history/notification writes. Recheck membership and parent relationships before commit; retry transient database conflicts within bounded limits.
