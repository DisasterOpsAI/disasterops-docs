## Frontend Routes

Each role gets its own route tree; UI guidance for Notifications is included.

### Affected Individuals (`/affected-individual`)

- **GET** `/affected-individual/public-request`

  - _UI:_ Full-page form with name, contact, geolocation picker, description, and “Add Media” (supports image/video/audio).

- **GET** `/affected-individual/chat/:roomId`

  - _UI:_ Chat view with message list, input box with attachment button.

### First Responders (`/first-responder`)

- **GET** `/first-responder/dashboard`

  - _UI:_

    - **Panels:** “Active Requests” list, “My Tasks” list.
    - **Header:** Bell icon with unread count badge (fetches `/api/users/:id/notifications`) ([digitalocean.com][4]).

- **GET** `/first-responder/requests` → `/api/requests`
- **GET** `/first-responder/requests/:requestId`

  - _UI:_ Detail page with edit form (PUT `/api/requests/:id`) and chat.

- **GET** `/first-responder/resources` → `/api/resources`
- **GET** `/first-responder/tasks` → `/api/tasks`
- **GET/PUT** `/first-responder/tasks/:taskId`
- **GET** `/first-responder/notifications` → `/api/users/:id/notifications`

  - _UI:_ Dropdown list showing each notification’s icon, message preview, timestamp; clicking marks it read (PUT `/api/notifications/:id/read`).

### Volunteers (`/volunteer`)

_Same as responders under `/volunteer/_`routes, including`/volunteer/notifications`.\*
