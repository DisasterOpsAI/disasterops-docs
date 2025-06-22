## Backend API Endpoints

### 1. Authentication & User Profiles

_No signup/login; frontend issues JWTs (e.g., via Auth0/Firebase Auth), backend verifies them._

- **POST** `/api/auth/verify`

  - **In:**

    ```json
    { "token": "<JWT>" }
    ```

  - **Out:**

    ```json
    {
      "userId": "uuid-1234",
      "role": "first_responder" | "volunteer"
    }
    ```

  - _Purpose:_ Validate JWT and return user identity & role ([learn.microsoft.com][5]).

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

  - _Purpose:_ Create first responder profile with specialties ([swagger.io][6]).

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

  - _Purpose:_ Create volunteer profile ([swagger.io][6]).

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
    { "status": "ok" }
    ```

  - _Purpose:_ Live-update user GPS coords via Firebase RTDB or Redis Streams ([redis.io][3]).

---

### 2. Help Requests

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

  - _Purpose:_ Register anonymous help request; supports extensible media ([notificationapi.com][7]).

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

  - _Purpose:_ Return all requests in one payload (no pagination) ([getambassador.io][8]).

- **PUT** `/api/requests/:requestId`

  - **In:**

    ```json
    {
      "additionalInfo": "Found another family needing aid.",
      "newAttachments": [
        { "type": "audio", "url": "https://…" },
        { "type": "image", "url": "https://…" }
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

  - _Purpose:_ Append details or media to a request ([stackoverflow.com][9]).

---

### 3. Resource Management

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

  - _Purpose:_ Return full inventory ([getambassador.io][8]).

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

  - _Purpose:_ Fetch single resource ([firebase.google.com][10]).

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

  - _Purpose:_ Add new resource via API ([medium.com][11]).

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

  - _Purpose:_ Update stock/status/coords ([redis.io][3]).

---

### 4. Task Assignment

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

  - _Purpose:_ Create a task linking request, assignee, and resources ([designgurus.io][1]).

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

  - _Purpose:_ Return all tasks ([getambassador.io][8]).

- **GET** `/api/tasks/:taskId`

  - **Out:**

    ```json
    {
      "taskId": "task-xyz",
      "requestId": "req-abc",
      "status": "in_progress",
      "notes": "Bridge is unstable",
      "attachments": [{ "type": "image", "url": "https://…" }],
      "assignedResponder": "uuid-1234",
      "createdAt": "2025-06-22T14:35:00Z"
    }
    ```

  - _Purpose:_ Fetch task detail with media-rich reporting ([stackoverflow.com][9]).

- **PUT** `/api/tasks/:taskId`

  - **In:**

    ```json
    {
      "status": "completed",
      "notes": "Delivered supplies safely.",
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

  - _Purpose:_ Update status, add notes/media ([stackoverflow.com][9]).

---

### 5. Communication Hub

- **(Internal)** **POST** `/api/chat/rooms`

  - _Invoked on request creation:_

    ```json
    {
      "requestId": "req-abc",
      "participants": ["anon-456"]
    }
    ```

- **GET** `/api/chat/rooms/:roomId`

  - **Out:**

    ```json
    {
      "roomId": "room-123",
      "requestId": "req-abc",
      "participants": ["anon-456", "uuid-1234"]
    }
    ```

  - _Purpose:_ Return room metadata ([stackoverflow.com][12]).

- **POST** `/api/chat/rooms/:roomId/messages`

  - **In:**

    ```json
    {
      "senderType": "affected" | "responder",
      "senderId": null | "uuid-1234",
      "text": "On my way",
      "attachments": [ { "type":"image","url":"https://…" } ],
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

  - _Purpose:_ Post chat messages; clients subscribe direct to DB/stream ([redis.io][3]).

---

### 6. Notifications

Triggered on events like task assignment, profile updates, etc.; delivered via FCM or Redis Streams ([firebase.google.com][2], [redis.io][3]).

- **POST** `/api/notifications`

  - **In:**

    ```json
    {
      "userId": "uuid-1234",
      "type": "task_assigned",
      "payload": {
        "taskId": "task-xyz",
        "message": "You’ve been assigned Task task-xyz"
      }
    }
    ```

  - **Out:**

    ```json
    {
      "notificationId": "notif-456",
      "sentAt": "2025-06-22T14:50:00Z"
    }
    ```

  - _Purpose:_ Create a notification; backend pushes via FCM or writes to Redis Stream ([firebase.google.com][10]).

- **GET** `/api/users/:userId/notifications`

  - **Out:**

    ```json
    [
      {
        "notificationId": "notif-456",
        "type": "task_assigned",
        "payload": { /* … */ },
        "read": false,
        "timestamp": "2025-06-22T14:50:00Z"
      },
      …
    ]
    ```

  - _Purpose:_ Fetch user’s notification feed ([designgurus.io][1]).

- **PUT** `/api/notifications/:notificationId/read`

  - **In:**

    ```json
    { "read": true }
    ```

  - **Out:**

    ```json
    {
      "notificationId": "notif-456",
      "readAt": "2025-06-22T14:55:00Z"
    }
    ```

  - _Purpose:_ Mark a notification as read .

---
