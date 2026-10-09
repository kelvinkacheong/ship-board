# Ship Lead · Board

A simple, interactive kanban board for tracking tasks. Hosted on GitHub Pages at [kelvinkacheong.github.io/ship-board](https://kelvinkacheong.github.io/ship-board/).

## Features

- **View mode**: Anyone can view the board without authentication
- **Edit mode**: With a GitHub token, you can add, edit, delete, and drag tasks between columns
- **Persistent storage**: Tasks are stored in `tasks.json` and synced via GitHub API
- **Mobile-friendly**: Responsive design with touch support for drag-and-drop

## tasks.json Schema

The board reads from `tasks.json` at the repo root. You can edit this file directly or use the web interface.

```json
{
  "lastUpdated": "2026-10-09T03:47:00Z",
  "tasks": [
    {
      "id": "task-001",
      "title": "Task title",
      "tags": ["Backend", "Portal"],
      "status": "in_progress",
      "progress": 40,
      "assignees": [
        { "name": "Kelvin", "initial": "K", "color": "#2e90fa" }
      ],
      "dueDate": "2026-10-13",
      "note": "BAU",
      "order": 0
    }
  ]
}
```

### Field Reference

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | Yes | Unique identifier (e.g., `task-001` or `task-1696841234567`) |
| `title` | string | Yes | Task title |
| `tags` | string[] | Yes | Array of tag labels (can be empty `[]`) |
| `status` | string | Yes | Column: `pending`, `assigned`, `in_progress`, `pending_further_actions`, or `shipped` |
| `progress` | number | Yes | 0-100 percentage |
| `assignees` | object[] | Yes | Array of assignee objects (can be empty `[]`) |
| `assignees[].name` | string | Yes | Display name |
| `assignees[].initial` | string | Yes | Single letter for avatar |
| `assignees[].color` | string | No | Hex color for avatar (default: `#b692f6`) |
| `dueDate` | string\|null | Yes | ISO date `YYYY-MM-DD` or `null` |
| `note` | string\|null | Yes | Optional note (e.g., "BAU", "Low priority") or `null` |
| `order` | number | Yes | Sort order within column (0-indexed) |

### Columns (in display order)

1. `pending` - Tasks not yet started
2. `assigned` - Tasks assigned but not started
3. `in_progress` - Work in progress
4. `pending_further_actions` - Blocked or waiting
5. `shipped` - Completed tasks

## Setting Up Edit Mode

To edit tasks on the live site, you need a GitHub fine-grained personal access token:

1. Go to [GitHub Fine-grained Tokens](https://github.com/settings/tokens?type=beta)
2. Click **Generate new token**
3. Name it (e.g., "Ship Board Editor")
4. Set **Expiration** as desired
5. Under **Repository access**, select **Only select repositories** → choose **kelvinkacheong/ship-board**
6. Under **Permissions** → **Repository permissions** → **Contents**: select **Read and write**
7. Click **Generate token** and copy it

On the board, click the **Edit** button and paste your token. It's stored only in your browser's localStorage.

To sign out: right-click the Edit button and confirm.

## Programmatic Editing

You can update tasks programmatically by modifying `tasks.json` directly via Git or the GitHub API. The web interface will pick up changes on next load.

Example using GitHub API:

```bash
# Get current file
curl -H "Authorization: Bearer YOUR_TOKEN" \
  https://api.github.com/repos/kelvinkacheong/ship-board/contents/tasks.json

# Update (include sha from GET response)
curl -X PUT -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{"message":"Update tasks","content":"BASE64_ENCODED_JSON","sha":"CURRENT_SHA"}' \
  https://api.github.com/repos/kelvinkacheong/ship-board/contents/tasks.json
```
