## Frontend Route Structure

Three separate route trees—one per role—serve tailored UIs; each page maps directly to the above endpoints. Layout ideas accompany each route to guide UX:

### Affected Individuals (`/affected-individual`)

- **GET** `/affected-individual/public-request`

  - _UI:_ Full-screen form with fields for name, contact, location picker, description textarea, and an “Add Media” button that opens a file selector supporting images/audio/video. Submitted attachments populate an array ([firebase.google.com][13]).

- **GET** `/affected-individual/chat/:roomId`

  - _UI:_ Simple chat view showing message bubbles and an upload bar for attachments; auto-scrolls on new messages.

### First Responders (`/first-responder`)

- **GET** `/first-responder/dashboard`

  - _UI:_ Two panels—“Active Requests” list with status badges, “My Tasks” list with progress bars and quick-action buttons. A top toolbar shows real-time map pins from `/api/users/:id/location` updates.

- **GET** `/first-responder/requests` → `/api/requests`

  - _UI:_ Card grid of help requests; clicking opens the request detail.

- **GET** `/first-responder/requests/:requestId`

  - _UI:_ Detail page with request info, attachments gallery, “Add Info” form matching `PUT /api/requests/:id`, and chat sidebar.

- **GET** `/first-responder/resources` → `/api/resources`

  - _UI:_ Table of resources with columns: Type, Quantity, Status, Location, lastUpdated; inline edit icons to invoke `PUT /api/resources/:id`.

- **GET** `/first-responder/tasks` → `/api/tasks`

  - _UI:_ Kanban board grouped by status; cards show taskId, request snippet, assigned resources.

- **GET/PUT** `/first-responder/tasks/:taskId`

  - _UI:_ Task detail with note editor, attachments uploader, status dropdown.

### Volunteers (`/volunteer`)

_Same structure & UI patterns as first responders but under `/volunteer/_`.\*

---

This comprehensive design equips your teams with clearly defined REST endpoints, media-flexible payloads, real-time capabilities, and UI guidance—streamlining both implementation and user experience.
