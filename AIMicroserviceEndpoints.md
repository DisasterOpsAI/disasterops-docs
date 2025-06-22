## AI Microservice Endpoints

- **POST** `/ai/parse-request`

  - **In:**

    ```json
    {
      "requestId": "req-abc",
      "text": "Need water at 34.05,-118.25",
      "attachments": [{ "type": "image", "url": "https://…" }]
    }
    ```

  - **Out:**

    ```json
    {
      "assignedResponder": "uuid-1234",
      "allocatedResources": [{ "resourceId": "res-001", "quantity": 100 }]
    }
    ```

  - _Purpose:_ Single orchestration call that NLP-parses, looks up available resources & personnel, creates a task, reserves supplies, and returns assignment details ([medium.com][7]).
