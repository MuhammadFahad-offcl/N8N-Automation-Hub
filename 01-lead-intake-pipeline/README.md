Lead Intake & Qualification System (n8n Workflow)
An automated, production-grade lead processing pipeline built on n8n that ingests web form submissions, normalizes payload data, updates a MySQL database using idempotent upsert queries, sends instant outreach via the Meta WhatsApp Cloud API, and closes the request loop with a structured HTTP response.
---
System Architecture & Core Capabilities
Synchronous Webhook Handling: Configured to keep the HTTP connection active while processing downstream nodes, ensuring immediate feedback upon execution.
Data Normalization: Strips invalid phone formatting, forces lowercase email handling, trims whitespaces, and appends UTC ISO timestamps.
Idempotent Storage: Employs an `INSERT ... ON DUPLICATE KEY UPDATE` strategy to update existing lead records without creating duplicate rows.
Automated Customer Outreach: Dispatches real-time WhatsApp confirmation messages via Meta's WhatsApp Cloud API.
---
Workflow Pipeline Breakdown
Step	Node Name	Node Type	Description
1	Webhook	`n8n-nodes-base.webhook`	Ingests `POST` payloads on the `/lead-intake` path.
2	Code in JavaScript	`n8n-nodes-base.code`	Normalizes fields, cleans phone numbers, and injects ISO timestamps.
3	Execute a SQL query	`n8n-nodes-base.mySql`	Runs an idempotent SQL upsert into the `leads` table.
4	Send message	`n8n-nodes-base.whatsApp`	Connects to the WhatsApp API to dispatch automated outreach messages.
5	Respond to Webhook	`n8n-nodes-base.respondToWebhook`	Returns HTTP 200 response to close the client request lifecycle.
---
Data Transformation Logic (JavaScript Node)
The JavaScript node executes the following transformation to sanitize inputs before database interaction:
```javascript
const body = $json.body;

return {
  json: {
    name: body.name?.trim() || "",
    email: body.email?.trim().toLowerCase() || "",
    phone: body.phone?.replace(/\s+/g, "") || "",
    message: body.message?.trim() || "",
    source: "landing_page",
    received_at: new Date().toISOString()
  }
};
```
Name & Message: Trims extraneous whitespace from string boundaries.
Email: Standardizes string casing to lowercase to preserve unique constraint enforcement.
Phone: Strips whitespace formatting to ensure compliance with E.164 phone string standards.
Timestamp: Injects a standardized UTC timestamp string formatted to ISO 8601.
---
Database Schema & SQL Execution
Execute this DDL statement in your MySQL database to create the required target table:
```sql
CREATE TABLE IF NOT EXISTS leads (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    phone VARCHAR(50),
    message TEXT,
    source VARCHAR(100),
    received_at DATETIME,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
The MySQL node uses the following parameterized query to execute record upserts:
```sql
INSERT INTO leads (name, email, phone, message, source, received_at)
VALUES (
  '{{ $json.name }}',
  '{{ $json.email }}',
  '{{ $json.phone }}',
  '{{ $json.message }}',
  '{{ $json.source }}',
  STR_TO_DATE('{{ $json.received_at }}', '%Y-%m-%dT%H:%i:%s')
)
ON DUPLICATE KEY UPDATE
  name = VALUES(name),
  phone = VALUES(phone),
  message = VALUES(message),
  source = VALUES(source),
  received_at = VALUES(received_at);
```
---
Deployment & Setup Instructions
Import Workflow JSON:
Open n8n and navigate to Workflows -> Import from File.
Select `Lead Intake & Qualification System.json`.
Configure Node Credentials:
MySQL Node: Configure target database host, port, credentials, and database name.
WhatsApp Node: Authenticate using your Meta Developer WhatsApp API credentials.
Activate Endpoint:
Switch the workflow toggle from Inactive to Active.
---
Testing & Verification
Verify the active endpoint by sending an HTTP POST request:
```bash
curl -X POST https://<your-n8n-instance>/webhook/lead-intake \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Muhammad Fahad",
    "email": "fahad@gmail.com",
    "phone": "+92304476543",
    "message": "Inquiring about AI automation"
  }'
```
---
License
This project is licensed under the MIT License.
