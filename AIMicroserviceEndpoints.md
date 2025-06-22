
## AI Microservice Endpoints

* **POST** `/ai/parse-request`

  * **In:**

    ```json
    {
      "requestId": "req-abc",
      "text": "Need water at 34.05,-118.25",
      "attachments": [ { "type": "image", "url": "https://…" } ]
    }
    ```
  * **Out:**

    ```json
    {
      "assignedResponder": "uuid-1234",
      "allocatedResources": [
        { "resourceId": "res-001", "quantity": 100 }
      ]
    }
    ```
  * *Purpose:* Orchestrate NLP parsing, resource/personnel lookup, task creation, resource reservation, and emit Notifications ([notificationapi.com][7]).
