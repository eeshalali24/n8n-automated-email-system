# AI-Powered Employee Task Email Automation

## Tech Stack
- **Automation:** n8n
- **AI / LLM:** OpenAI GPT-4.1
- **Data Source:** Google Sheets
- **Email Delivery:** Gmail
- **Authentication:** OAuth2 (Google Sheets, Gmail) and API credential (OpenAI)
- **Logic:** n8n expressions / workflow nodes

## Description

This workflow automates the process of notifying employees about newly assigned tasks. When a row in a Google Sheet is updated, the workflow reads the employee and task details, uses OpenAI GPT-4.1 to draft a professional, personalized email, and sends it automatically through Gmail.

It eliminates the need to manually write and send individual task notification emails, removing repetitive administrative work while keeping communication consistent in tone and structure across all employees.

## Overview

1. The **Google Sheets Trigger** polls the configured sheet every minute and detects when a row is updated (`rowUpdate` event).
2. The updated row's data — `Name`, `Email`, `Task`, and `Deadline` — is passed into the workflow.
3. An **OpenAI GPT-4.1** node receives this data and generates a professional, personalized email body, signed off using a sender name pulled from an environment variable.
4. The **Gmail** node sends the generated email to the employee's address, with the subject line built dynamically from the `Task` and `Deadline` fields.

## Problem It Solves

Manually drafting and sending task notification emails is a repetitive process that doesn't scale well, especially when tasks are tracked in a shared spreadsheet. Common pain points this workflow addresses:

- Manually copying task details from a spreadsheet into an email
- Delays between a task being logged and the employee being notified
- Inconsistent tone or formatting across notification emails
- Time spent on a communication step that doesn't require human judgment
- Risk of human error when transcribing task details

## Workflow Architecture

```mermaid
flowchart LR
    A[Google Sheets Trigger] --> B[OpenAI GPT-4.1]
    B --> C[Gmail]
```

`Google Sheets Trigger` → `OpenAI GPT-4.1` → `Gmail`

## Node Breakdown

### 1. Google Sheets Trigger
- Polls the configured Google Sheet every minute
- Listens for the `rowUpdate` event
- Passes row data (`Name`, `Email`, `Task`, `Deadline`) into the workflow

### 2. Message a Model — OpenAI GPT-4.1
- Uses the GPT-4.1 model
- Receives the employee's `Name`, `Email`, and `Task` from the trigger data
- System prompt instructs the model to act as an operations manager and professional business communicator
- Generates a personalized email body
- Signs off using a sender name retrieved from the `SENDER_NAME` environment variable rather than a hardcoded value

### 3. Send a Message — Gmail
- Recipient email is taken dynamically from the Google Sheets data
- Subject line is dynamically constructed from `Task` and `Deadline`
- The AI-generated output is used as the email body
- Sent as plain text
- n8n attribution footer is disabled (`appendAttribution: false`)

## Setup Instructions

1. **Google Sheets**
   - Connect your own Google Sheets OAuth2 credential.
   - Select/configure your own Google Sheet.
   - Ensure the sheet contains `Name`, `Email`, `Task`, and `Deadline` columns.

2. **OpenAI**
   - Connect your own OpenAI API credential.
   - Uses GPT-4.1.
   - Configure the `SENDER_NAME` environment variable in your n8n instance.

3. **Gmail**
   - Connect your own Gmail OAuth2 credential.
   - Recipient, subject, and body are populated dynamically — no further configuration needed.

4. **Environment Variables**
   - `SENDER_NAME` — name used in the generated email signature.

5. **Import**
   - Import the sanitized JSON workflow into your n8n instance.
   - Connect your own credentials and select your own Google Sheet.
   - Test with sample data before activating the workflow.

## Notes
- Intended for task/assignment data stored in Google Sheets.
- Email content is generated dynamically by the LLM, so wording will vary slightly between emails while maintaining a consistent professional style.
- This repository contains a sanitized workflow export.
- Sensitive credentials, personal information, private resource identifiers, webhook identifiers, and n8n instance-specific identifiers have been replaced with placeholders or removed where applicable.
- Users must configure their own credentials and resource IDs before running the workflow.
