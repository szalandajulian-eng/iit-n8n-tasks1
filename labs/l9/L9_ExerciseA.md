# L9, Exercise A – Webhook → Date and Time Formatting

## Goal
Create a workflow in **n8n** that receives an event name via a **Webhook**, adds a formatted timestamp, and returns a message in JSON.

---

## Step 1: Create a New Workflow
1. Start **n8n** in your fork Codespace. 
2. Add  **Workflows → + New**.
3. Name it: `L9_ExerciseA_<your_github_username>`.

NOTE: In the `L9/solutions/L9_ExerciseA.md provide a screenshots showing a newly created, properly named workflow.



## Step 2: Add a Webhook Node
1. Drag a **Webhook** node onto the canvas.
2. Configure:
   - **HTTP Method**: `GET`
   - **Path**: `event`
   - **Response Mode**: `Using 'Respond to Webhook node`
3. Save the workflow.

You now have two URLs:
- Test: `http://[YOUR_CODESPACE_URL]/webhook-test/event`
- Production: `http://[YOUR_CODESPACE_URL]/webhook/event`

---

## Step 3: Add a Code Node
1. Click **+** to add a new Node after Webhook.
2. Choose `Code` node from the list.
3. In the **Code** node, paste:

```javascript
const eventName = $json["query"]?.event || "Unknown Event";

const now = new Date();
const formatted = now.toISOString().replace("T", " ").substring(0, 16);

return [
  {
    json: {
      message: `Event "${eventName}" received at ${formatted}`
    }
  }
];
```

NOTE: Provide a screenshot with node configuration to your solution file. Address bar in the browser must be visible.

---

## Step 4: Add a Respond to Webhook Node
1. Click on  **+** and Add `Respond to Webhook` node to the canvas.
2. Configure:
   - **Response Code**: `200`
   - **Respond With**: `All incoming items`
4. Save.

NOTE: Provide a screenshot with node configuration to your solution file. Address bar in the browser must be visible. 
---

## Step 5: Test the Workflow
1. Click **Execute Workflow** to enable the test endpoint.
2. Test in a browser:

- Without parameter:  
  `http://[YOUR_CODESPACE_URL]/webhook-test/event`

- With event name:  
  `http://[YOUR_CODESPACE_URL]/webhook-test/event?event=Meeting`

3. Or use curl:
```bash
curl "http://[YOUR_CODESPACE_URL]/webhook-test/event?event=Lecture"
```

NOTE: Provide a screenshot with the result of workflow execution. Address bar in the browser must be visible.

---

## Note: Example Output

Example output should be like:

```json
{
  "message": "Event \"Lecture\" received at 2025-09-15 10:30"
}
```

---

## Step 7: Activate Workflow
You can also activate your workflow:

1. Toggle **Active** in the top-right corner.  
2. Use the production URL for permanent access:  
   `http://[YOUR_CODESPACE_URL]/webhook/event?event=Workshop`
