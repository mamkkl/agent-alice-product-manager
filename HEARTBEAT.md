# HEARTBEAT.md — Product Manager (Alice)

## OpenProject Mention Polling

1. Read `.heartbeat/watermark.json` if it exists (contains processed notification IDs)
2. Run: `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py list-notifications --reason mentioned --unread-only`
3. Output: `[HEARTBEAT] Fetched N unread mention(s), M new after watermark filter` where N is total unread and M is after filtering against watermark
4. For each notification NOT in the watermark:
   a. Get notification details: `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py get-notification --id <notification_id>`
   b. Get work package context: `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py get-work-package --id <wp_id>`
   c. Get recent comments: `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py list-comments --id <wp_id> --limit 10`
   d. Analyze the mention using your identity and role context
   e. Post a response: `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py add-comment --id <wp_id> --comment "<your response>"`
   f. Mark as read: `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py read-notification --id <notification_id>`
   g. Append the notification ID to `.heartbeat/watermark.json`
5. If no unprocessed notifications, output `[HEARTBEAT] 0 new mentions`

## Design Task Scanning

After processing notifications, scan for Features that need UI/UX design tasks:

1. Run: `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py list-work-packages --project kaironinv-dot-ai --status "Specified" --limit 50`
2. For each Feature in "Specified" status:
   a. Check if a child task with subject starting with `[UI/UX Design]` already exists (list related work packages or check comments)
   b. If NO design task exists yet:
      - Create a design task:
        ```
        python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py create-work-package \
          --project kaironinv-dot-ai \
          --subject "[UI/UX Design] <Feature Subject>" \
          --type Task \
          --description "Design UI/UX for Feature #<feature_id>: <Feature Subject>. Review the feature and its user stories, then create wireframes, interaction specs, and accessibility guidelines."
        ```
      - Assign to Pixel: `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py update-work-package --id <task_id> --assignee Pixel`
      - Link to parent Feature: `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py create-relation --from-id <feature_id> --to-id <task_id> --type includes`
      - Notify Pixel by commenting on the task with a mention
      - Output: `[HEARTBEAT] Created design task #<task_id> for Feature #<feature_id> and assigned to Pixel`
   c. If design task already exists, skip
3. Output: `[HEARTBEAT] Design scan complete — N new design task(s) created`

## Watermark File Format

Location: `.heartbeat/watermark.json`
```json
{
  "processed_notification_ids": [142, 143, 147],
  "last_poll_utc": "2026-04-12T00:00:00Z"
}
```
Create the `.heartbeat/` directory and file if they don't exist.
