## 1. Firebase Realtime Database Structure

### 1.1 `chatRooms`

All chat data lives under a top-level `chatRooms` node. Each `roomId` key contains the associated help-request and nested message objects. Keeping this flat prevents deep nesting and over-fetching ([firebase.google.com][1]).

```json
{
  "chatRooms": {
    "{roomId}": {
      "requestId": "req-abc",
      "participants": {
        "{userId}": true
      },
      "messages": {
        "{messageId}": {
          "senderType": "affected",
          "senderId": null,
          "text": "On my way",
          "timestamp": 1624368000000,
          "attachments": {
            "{attachmentId}": true
          }
        }
      }
    }
  }
}
```

- Keys are semantic (no auto-generated arrays) and values are booleans or primitives to optimize lookups ([geeksforgeeks.org][2]).

### 1.2 `liveLocations`

Store each responder’s most recent GPS update under a flat `liveLocations` node. Overwriting by `userId` keeps only the latest coords ([medium.com][3]).

```json
{
  "liveLocations": {
    "{userId}": {
      "lat": 34.0522,
      "lng": -118.2437,
      "timestamp": 1624368600000
    }
  }
}
```
