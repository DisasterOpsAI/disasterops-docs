## Backend API Endpoints

### 1. Authentication & User Profiles

All JWT issuance/verification happens in the frontend; the backend simply validates tokens and manages profile data ([stackoverflow.blog][2]).

- **POST** `/api/auth/verify`

  - **In:** `{ "token": "<JWT>" }`
  - **Out:**

    ```json
    {
      "userId": "uuid-1234",
      "role": "first_responder" | "volunteer"
    }
    ```

  - _Purpose:_ Confirm token validity and retrieve user identity & role ([learn.microsoft.com][1]).

- **POST** `/api/users/first-responders`

  - **In:**

    ```json
    {
      "userId": "uuid-1234",
      "name": "Jane Doe",
      "skills": ["water distribution", "first aid"],
      "contact": "jane@example.com"
    }
    ```

  - **Out:**

    ```json
    {
      "userId": "uuid-1234",
      "createdAt": "2025-06-22T14:00:00Z"
    }
    ```

  - _Purpose:_ Create a profile with skills & contact for a first responder ([stackoverflow.com][3]).

- **POST** `/api/users/volunteers`

  - **In:**

    ```json
    {
      "userId": "uuid-5678",
      "name": "John Smith",
      "skills": ["logistics", "cooking"],
      "contact": "john@example.com"
    }
    ```

  - **Out:**

    ```json
    {
      "userId": "uuid-5678",
      "createdAt": "2025-06-22T14:05:00Z"
    }
    ```

  - _Purpose:_ Create a volunteer profile with their specialties ([stackoverflow.com][3]).

- **PUT** `/api/users/:userId/location`

  - **In:**

    ```json
    {
      "lat": 34.0522,
      "lng": -118.2437,
      "timestamp": "2025-06-22T14:10:00Z"
    }
    ```

  - **Out:**

    ```json
    {
      "status": "ok"
    }
    ```

  - _Purpose:_ Live-update user GPS coordinates via Firebase RTDB or Redis Streams ([firebase.google.com][4]).

---

### 2. Help Requests

Affected individuals submit anonymously with arbitrary attachments; responders/volunteers can append details later ([speakeasy.com][5]).

- **POST** `/api/requests`

  - **In:**

    ```json
    {
      "name": "Alice Doe",
      "contactInfo": "alice@example.com",
      "location": { "lat": 34.05, "lng": -118.25 },
      "description": "Need drinking water",
      "attachments": [{ "type": "image", "url": "https://…" }]
    }
    ```

  - **Out:**

    ```json
    {
      "requestId": "req-abc",
      "chatRoomId": "room-123",
      "createdAt": "2025-06-22T14:15:00Z"
    }
    ```

  - _Purpose:_ Register a new anonymous help request with extensible media ([blog.xapihub.io][6]).

- **GET** `/api/requests`

  - **Out:**

    ```json
    [
      {
        "requestId": "req-abc",
        "description": "Need drinking water",
        "status": "pending",
        "attachments": [ /* same schema */ ],
        "createdAt": "2025-06-22T14:15:00Z"
      },
      …
    ]
    ```

  - _Purpose:_ Return all help requests in one payload (no pagination) ([medium.com][7]).

- **PUT** `/api/requests/:requestId`

  - **In:**

    ```json
    {
      "additionalInfo": "Found another family needing aid.",
      "newAttachments": [
        { "type": "image", "url": "https://…" },
        { "type": "audio", "url": "https://…" }
      ]
    }
    ```

  - **Out:**

    ```json
    {
      "requestId": "req-abc",
      "updatedAt": "2025-06-22T14:20:00Z"
    }
    ```

  - _Purpose:_ Append more context or media to an existing request ([stackoverflow.com][8]).

---

### 3. Resource Management

Supports listing all resources, single-resource updates, and creation; bulk updates can be added later ([medium.com][7]).

- **GET** `/api/resources`

  - **Out:**

    ```json
    [
      {
        "resourceId": "res-001",
        "type": "water_pallet",
        "quantity": 200,
        "unit": "liters",
        "location": { "lat": 34.05, "lng": -118.25 },
        "status": "available"
      },
      …
    ]
    ```

  - _Purpose:_ Return entire inventory in one call (no pagination) ([medium.com][7]).

- **GET** `/api/resources/:resourceId`

  - **Out:**

    ```json
    {
      "resourceId": "res-001",
      "type": "water_pallet",
      "quantity": 200,
      "location": { "lat": 34.05, "lng": -118.25 },
      "status": "available"
    }
    ```

  - _Purpose:_ Fetch details for a specific resource ([redis.io][9]).

- **POST** `/api/resources`

  - **In:**

    ```json
    {
      "type": "food_kit",
      "quantity": 50,
      "unit": "kits",
      "location": { "lat": 34.06, "lng": -118.24 }
    }
    ```

  - **Out:**

    ```json
    {
      "resourceId": "res-002",
      "createdAt": "2025-06-22T14:25:00Z"
    }
    ```

  - _Purpose:_ Add new supply types via API (no UI yet) ([swagger.io][10]).

- **PUT** `/api/resources/:resourceId`

  - **In:**

    ```json
    {
      "quantity": 180,
      "status": "in_transit",
      "location": { "lat": 34.07, "lng": -118.23 }
    }
    ```

  - **Out:**

    ```json
    {
      "resourceId": "res-001",
      "updatedAt": "2025-06-22T14:30:00Z"
    }
    ```

  - _Purpose:_ Update stock, status, or coordinates for one resource ([stackoverflow.com][11]).

---

### 4. Task Assignment

First responders/volunteers manage and annotate their tasks with notes and attachments ([stackoverflow.blog][2]).

- **POST** `/api/tasks`

  - **In:**

    ```json
    {
      "requestId": "req-abc",
      "assigneeId": "uuid-1234",
      "resourceAllocations": [{ "resourceId": "res-001", "quantity": 100 }]
    }
    ```

  - **Out:**

    ```json
    {
      "taskId": "task-xyz",
      "createdAt": "2025-06-22T14:35:00Z"
    }
    ```

  - _Purpose:_ Create a task linking a request, assignee, and resources ([medium.com][7]).

- **GET** `/api/tasks`

  - **Out:**

    ```json
    [
      {
        "taskId": "task-xyz",
        "requestId": "req-abc",
        "status": "pending",
        "createdAt": "2025-06-22T14:35:00Z"
      },
      …
    ]
    ```

  - _Purpose:_ Return all tasks in one call (no pagination).

- **PUT** `/api/tasks/:taskId`

  - **In:**

    ```json
    {
      "status": "in_progress",
      "notes": "Bridge is unstable.",
      "attachments": [{ "type": "image", "url": "https://…" }]
    }
    ```

  - **Out:**

    ```json
    {
      "taskId": "task-xyz",
      "updatedAt": "2025-06-22T14:40:00Z"
    }
    ```

  - _Purpose:_ Update task state and allow media-rich reporting ([speakeasy.com][5]).

---

### 5. Communication Hub

Chat rooms auto-spawn on request creation; messages flow via Firebase RTDB or Redis Streams ([redis.io][9]).

- **(Internal) POST** `/api/chat/rooms`

  - _Invoked by backend:_ `{ "requestId": "req-abc", "participants": ["anon-456"] }`.

- **GET** `/api/chat/rooms/:roomId`

  - **Out:**

    ```json
    {
      "roomId": "room-123",
      "requestId": "req-abc",
      "participants": ["anon-456", "uuid-1234"]
    }
    ```

  - _Purpose:_ Retrieve room metadata; access controlled by `roomId` or JWT.

- **POST** `/api/chat/rooms/:roomId/messages`

  - **In:**

    ```json
    {
      "senderType": "affected" | "responder",
      "senderId": null | "uuid-1234",
      "text": "On my way",
      "attachments": [ { "type": "image", "url": "https://…" } ],
      "timestamp": "2025-06-22T14:45:00Z"
    }
    ```

  - **Out:**

    ```json
    {
      "messageId": "msg-789",
      "createdAt": "2025-06-22T14:45:00Z"
    }
    ```

  - _Purpose:_ Post chat messages; clients subscribe directly to DB/stream for real-time updates ([reddit.com][12]).
