
# n8n Slack Automation for Wazuh Alerts

## 📌 Overview
This project demonstrates an automated Security Orchestration, Automation, and Response (SOAR) pipeline. It captures security alerts from Wazuh via webhooks and forwards them in real-time to a designated Slack channel using **n8n** (node-based workflow automation tool).

## 🚀 Prerequisites
* **Docker** installed on your system.
* A **Slack Workspace** with permissions to create apps.
* **Wazuh Manager** (optional, for final integration).

---

## 🛠️ Step-by-Step Implementation Guide

### Step 1: n8n Docker Setup
Run n8n locally using Docker. Execute the following command in your terminal:

```bash
docker run -it --rm \
  --name n8n \
  -p 5678:5678 \
  -e N8N_ENFORCEMENT_AGREEMENT=true \
  -v n8n_data:/home/node/.n8n \
  n8nio/n8n
```

### Step 2: Access n8n & Create a Webhook
1. Open your browser and navigate to `http://localhost:5678`.
2. Create a new workflow.
3. Click the **+** button, search for **Webhook**, and add the node.
4. Set the **HTTP Method** to `POST`.
5. Copy the **Test URL**.
6. Click on **Listen for test event** to keep it active.

### Step 3: Configure Slack Bot & Generate Token
To send automated messages, we need to create a Slack app with the appropriate permissions.

**1. Create the Slack App:**
* Navigate to the [Slack API Dashboard](https://api.slack.com/apps).
* Click **Create New App** > **From scratch**.
* Give it a meaningful name (e.g., `Wazuh-Alert-Bot`) and select your target workspace.

**2. Assign Permissions (Scopes):**
* From the left-hand menu, select **OAuth & Permissions**.
* Scroll down to the **Scopes** section.
* Under **Bot Token Scopes**, click "Add an OAuth Scope" and add `chat:write` (this allows the bot to post messages).

**3. Install and Generate Token:**
* Scroll back to the top of the page and click **Install to Workspace**, then click **Allow**.
* Copy the **Bot User OAuth Token** (it starts with `xoxb-`). Save this securely, as you will need it for n8n.

**4. Prepare the Slack Channel:**
* Open your Slack client and create a dedicated channel (e.g., `#wazuh-alerts`).
* Invite your newly created bot to this channel by typing `/invite @Wazuh-Alert-Bot` in the chat.

### Step 4: Integrate Slack in n8n Workflow
Now, we will configure n8n to format the incoming Wazuh data and push it to Slack.

**1. Add the Slack Node:**
* Go back to your active n8n workflow.
* Click the **+** icon next to your Webhook node, search for **Slack**, and add it.

**2. Authenticate the Connection:**
* In the Slack node settings, set **Authentication** to `Access Token`.
* Click to create a new credential, paste the `xoxb-...` token you copied in Step 3, and save it.

**3. Configure the Action:**
* **Resource:** Select `Message`.
* **Operation:** Select `Post`.
* **Channel:** Enter your exact channel name (e.g., `#wazuh-alerts`).

**4. Map the Dynamic Data:**
* In the **Text** field, use n8n expressions to format the incoming JSON data from Wazuh. You can use a structured template like this:

```text
🚨 *Critical Wazuh Alert Detected!* 🚨
*Rule Description:* {{$json.body.rule.description}}
*Severity Level:* {{$json.body.rule.level}}
*Agent Name:* {{$json.body.agent.name}}
```

* Save the node and toggle the workflow at the top right to **Active**.

### Step 5: Test the Pipeline via API (cURL)
To verify that the webhook is receiving data and forwarding it to Slack, send a POST request using your terminal. 

*Replace `YOUR_WEBHOOK_ID` with the actual ID from your n8n Webhook Test URL.*

```bash
curl -X POST http://localhost:5678/webhook-test/YOUR_WEBHOOK_ID \
     -H "Content-Type: application/json" \
     -d '{
           "rule": {
             "description": "Critical: SSH Brute Force Attempt Detected",
             "level": 10
           },
           "agent": {
             "name": "Web-Server-01",
             "ip": "192.168.1.100"
           }
         }'
```

If successful, you will instantly receive a formatted message in your designated Slack channel!

---

## 👨‍💻 Author
**Md. Shafiur Rahman**
* GitHub: [@MdShafiurRahman0](https://github.com/MdShafiurRahman0)
* Email: shafiur.rahman.shadat@gmail.com
```

**সংক্ষিপ্ত ব্যাখ্যা:** গিটহাবের README ফাইলে কোড ব্লকগুলো (
