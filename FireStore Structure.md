## 2. Firestore Structure

Firestore holds your main entities in separate collections. Each document is shallow (no deep nesting) to support targeted queries and indexing ([firebase.google.com][4]).

### 2.1 `users` (collection)

Profiles for responders/volunteers:

```
users/{userId}:
  name: string
  role: "first_responder" | "volunteer"
  skills: array<string>
  contact: string
  createdAt: timestamp
```

- Use subcollections (e.g., `notifications`) rather than nested maps for lists ([medium.com][5]).

### 2.2 `requests` (collection)

Help requests submitted by affected individuals:

```
requests/{requestId}:
  name: string
  contactInfo: string
  location: geopoint
  description: string
  status: "pending" | "triaged" | "closed"
  attachmentsMap: map<string, { type: string, url: string }>
  createdAt: timestamp
  updatedAt: timestamp
```

- `attachmentsMap` lets you reference any media without Firestore arrays ([reddit.com][6]).

### 2.3 `resources` (collection)

Inventory items:

```
resources/{resourceId}:
  type: string
  quantity: number
  unit: string
  location: geopoint
  status: "available"|"in_transit"|"depleted"
  updatedAt: timestamp
```

- Keep each document under 1 MiB; offload large histories to subcollections if needed ([estuary.dev][7]).

### 2.4 `tasks` (collection)

Assignments linking requests to responders:

```
tasks/{taskId}:
  requestId: string
  assigneeId: string
  resourceAllocations: array<{ resourceId: string, quantity: number }>
  status: "pending"|"in_progress"|"completed"
  notes: string
  attachmentsMap: map<string,{type:string,url:string}>
  createdAt: timestamp
  updatedAt: timestamp
```

- Denormalize often-queried fields (e.g., `assigneeName`) into documents to reduce lookups ([medium.com][8]).

### 2.5 `notifications` (subcollection or top-level)

User notifications:

```
users/{userId}/notifications/{notificationId}:
  type: "task_assigned" | "profile_updated" | …
  payload: map<string, any>
  read: boolean
  timestamp: timestamp
```

- Storing under each user supports ACLs and targeted queries ([firebase.google.com][9]).
