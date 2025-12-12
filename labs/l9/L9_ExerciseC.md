# L9, Exercise C – Webhook → Save to Fil

Create a workflow in **n8n** that receives a note via a **Webhook** parameter, adds a timestamp, and appends the text to a file stored in your Codespace (`/data/notes.txt`).

---

## Step 1: Create a New Workflow
1. Start **n8n** in your fork Codespace.  
2. Add **Workflows → + New**.  
3. Name it: `L9_ExerciseC_<your_github_username>`.

**NOTE:** In the `L9/solutions/L9_ExerciseC.md` provide a screenshot showing a newly created, properly named workflow. Address bar in the browser must be visible.

---

## Step 2: Add a Webhook Node
1. Drag a **Webhook** node onto the canvas.  
2. Configure:  
   - **HTTP Method**: `GET`  
   - **Path**: `note`  
   - **Response Mode**: `Using 'Respond to Webhook' node`  
3. Save the workflow.  

You now have two URLs:  
- Test: `http://[YOUR_CODESPACE_URL]/webhook-test/note`  
- Production: `http://[YOUR_CODESPACE_URL]/webhook/note`

---

## Step 3: Add a Code Node
1. Click **+** to add a new node after Webhook.  
2. Choose **Code** from the list.  
3. Paste the following code:

```javascript
const note = $json.query?.note ?? "Empty note";
const formatted = new Date().toISOString().replace("T"," ").substring(0,16);
const line = `${formatted} - ${note}\n`;

return [{
  json: { saved: true, preview: line },
  binary: {
    data: {
      data: Buffer.from(line, 'utf8').toString('base64'),
      mimeType: 'text/plain',
      fileName: 'notes.txt',
    }
  }
}];
```

## Step 4: Add a Write Binary File Node

Add a new node: **Read/Write Files from Disk**.

Configure:

`File Path and Name: ./data/notes.txt`

`Input Binary Field: data`

`Options → Append: true`

`Append: true` ensures every new note is appended instead of overwriting the file.

Make sure that the `data` folder exists in the path, which you use to start n8n.

## Step 5: Add a Respond to Webhook Node

Add Respond to Webhook node.

Configure:

`Response Code: 200`

`Respond With: JSON`

`Response Body:`

```
{ 
  "status": "ok", 
  "file": "/data/notes.txt" 
}
```

NOTE: Provide screenshots of node configuration to your solution file. Address bar in the browser must be visible.

## Step 6: Test the Workflow

Click Execute Workflow to enable the test endpoint.

Open a browser or run curl:

Without parameter:
`http://[YOUR_CODESPACE_URL]/webhook-test/note`

With parameter:
`http://[YOUR_CODESPACE_URL]/webhook-test/note?note=MyFirstNote`

Check that the workflow responds with confirmation JSON.

## Step 7: Verify in Codespace

Open the `./data/notes.txt` file in your Codespace.

Confirm that each request added a new line with timestamp and note, for example:

```
2025-09-16 12:45 - MyFirstNote
2025-09-16 12:46 - Another note
```

NOTE: Provide a screenshot of the file content in your Codespace editor.

## Step 8: Activate Workflow

Optionally, activate your workflow for permanent use:

Toggle Active in the top-right corner.

Use the production URL for notes logging:
`http://[YOUR_CODESPACE_URL]/webhook/note?note=Important`