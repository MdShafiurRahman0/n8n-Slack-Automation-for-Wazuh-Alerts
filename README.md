
# Slack Bot Setup & n8n Webhook Integration Guide

This guide covers the detailed process of creating a Slack Bot, generating an Auth Token, and connecting it to n8n for automated channel management and alerting.

---

## 🛠️ Part 1: Slack App & Bot Token Setup

Slack-e bot toiri korar jonno nicher steps gulo follow korun:

1. **Create App:** Go to [api.slack.com/apps](https://api.slack.com/apps) and click **Create New App** -> **From scratch**. App-er ekta nam din ebong workspace select korun.
2. **Configure Scopes:** Sidebar theke **OAuth & Permissions**-e jan. Niche **Scopes** section-e giye "Bot Token Scopes"-e nicher permission gulo add korun:
   - `channels:manage` (Channel toiri korar jonno)
   - `chat:write` (Message pathanor jonno)
   - `groups:write` (Private channel manage korar jonno)
3. **Install App:** Page-er upore giye **Install to Workspace** click korun ebong allow korun.
4. **Copy Token:** Install hoye gele apni ekta **Bot User OAuth Token** paben (jeta `xoxb-` diye shuru hoy). Eti copy kore n8n-er jonno save korun.

---

## 🚀 Part 2: n8n Webhook & Slack Integration Steps

n8n-e workflow setup korar detail process:

<Steps>
{/* Reason: Procedural dependency is critical here — credentials must exist before the node can function. */}
  <Step title="Webhook Node Setup" subtitle="Receiving Wazuh Data">
    n8n-e prothome ekta **Webhook Node** add korun. Method `POST` select korun ebong path din (e.g., `wazuh-alerts`). Test URL-ti copy kore Wazuh-er `ossec.conf`-e add korun.
  </Step>
  <Step title="Slack Credentials" subtitle="Connecting n8n to Slack">
    n8n-e **Slack Node** add korun. 'Credential for Slack' section-e 'New Credential' select korun. Authentication method **Access Token** din ebong Slack theke copy kora `xoxb-` token-ti paste korun.
  </Step>
  <Step title="Channel Creation Logic" subtitle="Resource & Operation">
    Jodi channel create korte chan:
    - **Resource:** Channel
    - **Operation:** Create
    - **Name:** Alert theke pawa data (e.g., `alert-{{ $json.id }}`)
  </Step>
  <Step title="Send Message" subtitle="Formatting Alert">
    Channel create hoye gele arekta Slack node diye message pathan. 
    - **Resource:** Message
    - **Operation:** Post
    - **Channel:** Agerto node theke pawa ID ba manual ID.
  </Step>
</Steps>

---

## 🧪 Testing and Commands

Apnar setup thik ache kina check korar jonno n8n-er Webhook URL-e nicher command-ti diye test alert pathan:

```bash
curl -X POST http://YOUR_N8N_IP:5678/webhook-test/wazuh-alerts \
     -H "Content-Type: application/json" \
     -d '{
           "id": "101",
           "rule": {"description": "Suspicious Login Detected"},
           "agent": {"name": "DB-Server"}
         }'
```

> **Note:** Slack Bot-ke oboshoy channel-e **Invite** korte hobe (`/invite @YourBotName`) jodi channel-ti bot-er toiri na hoy.
```

<Elicitations message="Slack automation-ke aro advance korte nicher topic gulo dekhte paren:">
  <Elicitation label="Slack-e interactive 'Action Buttons' add kora" query="How to add interactive buttons in Slack messages via n8n to acknowledge or close a Wazuh alert?" />
  <Elicitation label="Multiple channel-e conditional routing kora" query="How to route Wazuh alerts to different Slack channels based on rule level or agent group in n8n?" />
</Elicitations>
