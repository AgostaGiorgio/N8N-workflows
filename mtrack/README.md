# MTRACK - Expense Tracker & Alert System

**MTRACK** is an n8n workflow designed to automate personal expense tracking by parsing bank SMS notifications. The system ingests data, analyzes it, archives it in PostgreSQL, and provides real-time alerts and daily reports via Telegram.

## 🧠 Workflow Logic

The workflow handles three main logic streams:

1.  **Ingestion & Parsing (Webhook):**
    * Receives a JSON payload containing the SMS text and the date.
    * Parses the text (optimized for **Emirates NBD** format) to extract: amount, description, last digits of the card, and remaining credit limit.
    * Saves the transaction to **PostgreSQL**.
2.  **Security Check (Real-time):**
    * Compares the credit card limit extracted from the SMS against the expected limit calculated from the database history.
    * If there is a discrepancy (indicating missed transactions or errors), it sends an **Immediate Alert via Telegram**.
3.  **Reporting (Scheduled):**
    * Every evening at **22:00**, it queries the DB for the day's expenses.
    * Sends a summary report to Telegram, grouped by credit card, including a grand total.

## 🛠️ Requirements

* **n8n** (Self-hosted or Cloud)
* **PostgreSQL** (with `pgcrypto` extension enabled for UUID generation)
* **Telegram Bot** (Token and Chat ID)

## 📦 Installation & Setup

1.  Import the `workflow.json` file into n8n.
2.  Configure the **Credentials** in the nodes:
    * **Postgres account:** Connection details for your database.
    * **Telegram API:** Your bot token.
3.  **DB Initialization:**
    * The workflow includes a node to automatically create the table. Manually execute the `create` node once to generate the `transactions` table.

### Database Schema
The `transactions` table is created with the following structure:
```sql
CREATE TABLE transactions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  description TEXT,
  amount NUMERIC(12, 2) NOT NULL,
  card TEXT,
  card_limit NUMERIC(12, 2) NOT NULL
);
```

## 🔌 API Usage (Webhook)

To insert a new expense, send a `POST` request to the webhook endpoint.

**URL:** `https://<your-n8n-instance>/webhook/mtrack`
**Method:** `POST`
**Body:**

```json
{
  "sms": "Purchase of AED 150.00 with Card ending 1234 at SUPERMARKET. Available Limit is 9000.00.",
  "date": "30 gen 2026, 14:25"
}
```
*Note: The regex parsing is specifically configured for the Emirates NBD SMS format and handles months in Italian/English as configured in the 'extract info' node.*

## 🚨 Alerts & Notifications

* **Discrepancy Alert:** Triggered if `(Previous Limit - New Amount) != New Limit from SMS`.
* **Daily Report:** A daily recap sent automatically at 22:00.

---
*Workflow version: 1.0.0*