# Email Marketing Automation (EMA Demo)

A student/demo project to build and experiment with automated email marketing workflows, Mailchimp integrations, segmentation and webhook-based tracking.

This repository contains a responsive landing page and examples for integrating with the Mailchimp Marketing API and handling webhooks for events (opens, clicks, bounces, unsubscribes). Use this project as a base to implement backend automation, store events, and build analytics.

> NOTE: This README includes local development steps and a summary. For detailed deployment steps, see DEPLOY.md.

Table of contents
- Project overview
- Features
- Tech stack (suggested)
- Local development
- Environment variables
- Mailchimp setup
- Webhooks
- Contributing
- License

Project overview

This project demonstrates how to:
- Create automated email campaigns (welcome flows, abandoned-cart reminders)
- Use Mailchimp's Marketing API to create and manage campaigns and audiences
- Receive webhook events from Mailchimp and persist them for analytics
- Segment contacts and run conditional automation flows

Features (demo)
- Responsive landing page with hero, features, example workflows and contact form
- Static assets (index.html, styles.css) to showcase UI
- Guidance and examples for Mailchimp API usage and webhook handling

Suggested tech stack
- Frontend: static HTML/CSS (or React for an interactive UI)
- Backend (optional): Node.js + Express
- Database: PostgreSQL (or SQLite for a simple local demo)
- Queue / Scheduler: Redis + Bull (or Celery for Python)
- Mail provider: Mailchimp Marketing API + webhooks

Local development

Prerequisites
- Node.js (LTS) and npm installed
- (Optional local DB) PostgreSQL or SQLite
- (Optional) Redis if you plan to run background jobs

Quick start (static site)
1. Serve the static site locally (simple):
   - Option A: Use a static server (e.g., npm i -g serve) then run: serve .
   - Option B: Open index.html in a browser.

Quick start (backend example - Node.js)
1. Create a new Node.js project (if not present):
   - npm init -y
   - Install recommended deps: npm i express dotenv @mailchimp/mailchimp_marketing body-parser pg
2. Create an .env file using the example below and set your credentials.
3. Implement a small Express server to:
   - Expose an endpoint to receive Mailchimp webhooks (POST /webhooks/mailchimp)
   - Provide endpoints to create campaigns, manage subscribers and trigger automations (optional)
4. Start server: NODE_ENV=development node server.js (or use nodemon)

Environment variables (.env.example)

MAILCHIMP_API_KEY=your_mailchimp_api_key
MAILCHIMP_SERVER_PREFIX=usX                # the datacenter prefix from your API key, e.g. us19
DATABASE_URL=postgres://user:pass@host:5432/dbname
REDIS_URL=redis://localhost:6379
JWT_SECRET=a_long_random_secret
PORT=3000

Mailchimp setup
1. Create a Mailchimp account and an Audience (list) for your subscribers.
2. Generate an API Key: Account -> Extras -> API keys.
   - Note the server prefix (server part after the dash in the key), e.g. "us19".
3. (Optional) Create a test Audience and add sample contacts.
4. Use the Mailchimp Marketing API to create campaigns and set content. Example (Node.js):

const mailchimp = require('@mailchimp/mailchimp_marketing');
mailchimp.setConfig({ apiKey: process.env.MAILCHIMP_API_KEY, server: process.env.MAILCHIMP_SERVER_PREFIX });

async function createCampaign(listId, subject, fromName, replyTo, htmlContent) {
  const campaign = await mailchimp.campaigns.create({
    type: 'regular',
    recipients: { list_id: listId },
    settings: { subject_line: subject, title: subject, from_name: fromName, reply_to: replyTo }
  });

  await mailchimp.campaigns.setContent(campaign.id, { html: htmlContent });
  return campaign;
}

Webhooks
1. Configure Mailchimp webhooks in your Audience settings to point to: https://your-app.com/webhooks/mailchimp
2. Mailchimp posts application/x-www-form-urlencoded content (and sometimes JSON depending on settings). Ensure your webhook endpoint can parse form-encoded payloads.
3. Persist useful events in an events table (user_id, campaign_id, event_type, payload, timestamp) for analysis.

Security
- Do NOT commit secrets or API keys. Use environment variables or a secret manager.
- Validate incoming webhook requests if possible (Mailchimp includes an md5 field for some security checks).

Contributing
- Fork the repo, create a branch for your feature, open a PR with a clear description.
- Include tests for server logic if you add backend functionality.

License
- Use an appropriate license (MIT is common for demos). Add a LICENSE file if needed.

Contact
- Repo owner / maintainer: @samarakurami12
