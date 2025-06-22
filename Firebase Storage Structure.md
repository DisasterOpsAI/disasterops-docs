## 3. Firebase Storage Structure

Use a single bucket with logical folder prefixes. This follows “folder-per-entity” best practices for security rules and lifecycle management ([reddit.com][10]).

```
gs://<bucket>/
  requests/
    {requestId}/
      attachments/
        {attachmentId}.{ext}
  tasks/
    {taskId}/
      attachments/
        {attachmentId}.{ext}
  users/
    {userId}/
      profile.{ext}
```

- Store only references (URLs & metadata) in your database; let Storage handle binary data ([stackoverflow.com][11]).
