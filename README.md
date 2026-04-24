# Wazuh to Slack Alert Automation via n8n

This repository contains the configuration for a security automation pipeline that forwards critical Wazuh alerts to a Slack channel using n8n. This ensures your security team gets real-time notifications for high-priority events.

---

## 🧠 System Logic & Workflow

1.  **Event Detection (Wazuh):** Wazuh Manager detects a security event (e.g., brute force, file integrity change).
2.  **Trigger (Webhook):** If the alert level meets the threshold (e.g., Level >= 10), Wazuh sends a POST request to the n8n Webhook URL.
3.  **Data Parsing (n8n):** n8n extracts key info: Agent Name, Rule Description, Severity Level, and Source IP.
4.  **Slack Integration:** n8n sends a formatted "Incoming Security Alert" message to a designated Slack channel.

---

## 📋 Prerequisites

* **Wazuh Manager:** Access to `ossec.conf`.
* **n8n Instance:** Running (Docker/Cloud).
* **Slack Workspace:** Permissions to create an App or use Incoming Webhooks.

---

## 🚀 Step-by-Step Implementation

### 1. Setup Slack App & Webhook
1. Go to [api.slack.com/apps](https://api.slack.com/apps) and create a **New App**.
2. Enable **Incoming Webhooks**.
3. Click **Add New Webhook to Workspace** and select your security channel.
4. **Copy the Webhook URL.**

### 2. Configure n8n Workflow
1.  **Webhook Node:** Create a `POST` webhook. Note the URL.
2.  **Slack Node:** * **Resource:** Message
    * **Operation:** Post
    * **Authentication:** Use Slack OAuth or the Webhook URL from Step 1.
    * **Text:** ```text
        🚨 *Critical Security Alert* 🚨
        *Rule:* {{ $json.body.rule.description }}
        *Level:* {{ $json.body.rule.level }}
        *Agent:* {{ $json.body.agent.name }}
        *IP:* {{ $json.body.data.srcip }}
        ```

### 3. Wazuh Manager Configuration
Open your Wazuh config file:
```bash
sudo nano /var/ossec/etc/ossec.conf
```

Add this block:
```xml
<integration>
  <name>custom-n8n-slack</name>
  <hook_url>http://YOUR_N8N_IP:5678/webhook/wazuh-to-slack</hook_url>
  <level>10</level>
  <alert_format>json</alert_format>
</integration>
```

**Restart Wazuh:**
```bash
sudo systemctl restart wazuh-manager
```

---

## 🧪 Testing the Integration

Test the connection from the Wazuh server using `curl`:

```bash
curl -X POST http://YOUR_N8N_IP:5678/webhook/wazuh-to-slack \
     -H "Content-Type: application/json" \
     -d '{
           "rule": {"level": 12, "description": "SSH Brute Force Attempt"},
           "agent": {"name": "Production-Server-01"},
           "data": {"srcip": "192.168.1.50"}
         }'
```

Check your Slack channel; you should see a formatted alert instantly!

---

## 📌 Optimization Tips
* **Level Filtering:** Use Level 12+ for Slack to avoid "alert fatigue."
* **Color Coding:** In n8n, you can use "Attachments" in the Slack node to make the message sidebar **RED** for critical and **YELLOW** for warnings.
```

---

> **Pro-Tip:** Jodi n8n node-e Slack message pathate somossa hoy, tobe Slack-er **"Block Kit Builder"** use kore aro sundor visual design toiri korte paren (Buttons, Images, Divider lines shoho).

<FollowUp label="Slack alert-e 'Critical' hole Red ar 'Warning' hole Yellow color logic add korbo kivabe?" query="How to use n8n Switch and Slack Block Kit to color-code Wazuh alerts by severity (Red for Critical, Yellow for Warning)?" />
