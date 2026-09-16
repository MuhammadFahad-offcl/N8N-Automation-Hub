# Lead Intake & Qualification System (n8n Workflow)

An automated, production-ready lead intake pipeline built with **n8n**, **MySQL**, and the **WhatsApp Cloud API**. This workflow receives incoming landing page lead payloads, normalizes and sanitizes user input, upserts records to a MySQL database, triggers instant WhatsApp buyer outreach, and returns structured HTTP responses.

---

## 📐 System Architecture & Flowchart

```mermaid
flowchart TD
    Client[🌐 Web Client / Landing Page]
    
    subgraph n8n_Engine["n8n Workflow Engine"]
        Webhook["⚡ 1. Webhook Trigger<br/><code>POST /lead-intake</code>"]
        JSCode["JS 2. Code Node<br/><i>Sanitize & Standardize Payload</i>"]
        MySQL["🐬 3. MySQL Node<br/><i>Upsert Lead Record</i>"]
        WhatsApp["💬 4. WhatsApp Cloud API<br/><i>Send Instant Message</i>"]
        Respond["↩️ 5. Respond to Webhook<br/><i>HTTP 200 Confirmation</i>"]
    end

    DB[(MySQL Database<br/><code>leads</code> table)]
    MetaAPI[Meta WhatsApp Cloud API]

    Client -->|1. HTTP POST Payload| Webhook
    Webhook -->|Raw JSON| JSCode
    JSCode -->|Normalized JSON| MySQL
    MySQL -->|SQL Query| DB
    DB -->|Query OK| MySQL
    MySQL -->|Lead Payload| WhatsApp
    WhatsApp -->|API Request| MetaAPI
    MetaAPI -->|Delivery OK| WhatsApp
    WhatsApp -->|Complete| Respond
    Respond -->|200 OK Response| Client
