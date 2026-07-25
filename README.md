<div align="center">
<img src="./banner.png" width="140" height="140" style="border-radius:50%; object-fit:cover;" alt="No-Code-AI-Automation"/>

# Form Response Automation (n8n)

This n8n workflow automatically summarizes and categorizes new Google Form submissions using Google Gemini, then writes the result back into the Google Sheet.

## What it does

1. **Google Sheets Trigger** – Watches a connected Google Sheet (linked to a Google Form) and fires whenever a new row is added.
2. **Skip Blank Rows** – Filters out empty/incomplete rows (checks that the `Timestamp` field is not empty).
3. **Message a model (Gemini)** – Sends the submitted `Name`, `Email`, `Phone`, and `Message` to Gemini, asking it to:
   - Write a 2–3 sentence summary
   - Categorize the submission as `Urgent`, `General`, or `Spam`
   - Return the result as strict JSON
4. **Edit Fields** – Parses the JSON response from Gemini into two separate fields: `summary` and `category`.
5. **Update row in sheet** – Writes the result back into the same row in the Google Sheet, matched by `Timestamp`, updating the `Summary` column with `[Category] Summary text`.

## Requirements

- An active **Google Sheets** connection (OAuth2) for both the trigger and the update step.
- A **Google Gemini API** credential (Google AI Studio / PaLM API key).
- The target sheet must have the following columns: `Timestamp`, `Name`, `Email`, `Phone`, `Message`, `Summary`.

## Setup

1. Import `Automation.json` into your n8n instance.
2. Reconnect the credentials:
   - `Google Sheets Trigger account`
   - `Google Sheets account`
   - `Google Gemini (PaLM) Api account`

   > Credentials are **not** included in this export for security reasons — you must set them up manually in your own n8n instance.
3. In the **Message a model** node, confirm the `Model` field is set to a valid, currently available Gemini model (e.g. `gemini-1.5-flash` or `gemini-2.0-flash`). The exported file may reference an invalid/placeholder model name that needs to be reselected from the dropdown.
4. Point the trigger and update nodes at your own Google Sheet (`documentId` / `sheetName`).
5. Activate the workflow.

## Notes

- Free-tier Gemini API keys are rate-limited (commonly ~20 requests/minute). If you expect high form volume, consider enabling billing on your Google Cloud project or adding a delay/throttling step before the Gemini node.
- If you see a `Forbidden` or `denied access` error, check that billing/API access is properly enabled on the Google Cloud project tied to your Gemini credential.

## Workflow Diagram

```
Google Sheets Trigger → Skip Blank Rows → Message a model (Gemini) → Edit Fields → Update row in sheet
```
