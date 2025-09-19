# Customer Guide Template: Connect Your App to Google Sheets (OAuth 2.0)

This customer‑facing Markdown walks end users through creating a Google Cloud account, setting up OAuth 2.0 credentials (Client ID & Secret), and connecting those credentials to your app. You can ship this as‑is and customize the placeholders.

Replace items in `<ANGLE_BRACKETS>` with your app's details.

## Who this is for

Anyone who wants `<APP_NAME>` to read or write their Google Sheets securely using OAuth 2.0.

## What you'll do (high‑level)

- Create a Google Cloud account (free tier available)
- Create a Google Cloud project
- Configure the OAuth consent screen
- Create an OAuth 2.0 Client ID & Secret
- Enable the Google Sheets API (and optionally Drive)
- Paste the credentials into `<APP_NAME>` and connect

## Prerequisites

- A personal or work Google account
- Optional: Ability to approve/verify an OAuth app in your organization if using Google Workspace
- `<APP_NAME>` admin or integration settings access

## Step 0 — Create your Google Cloud account

1. Go to https://console.cloud.google.com/ and sign in with your Google account.
2. If you've never used Google Cloud, you'll be prompted to accept terms and optionally add billing (you can usually proceed on free tier for this setup).
3. Complete any required profile steps.

> **Note:** You can delete projects later; billing is only required for some advanced APIs—not for basic Sheets usage.

## Step 1 — Create a Google Cloud project

1. In the top nav, click the project selector → **New Project**.
2. Name it (e.g., `sheets-for-<APP_NAME>-<YOUR_NAME>`).
3. Click **Create** and ensure it's selected (look for the project name in the header).

## Step 2 — Enable APIs

1. Left menu → **APIs & Services** → **Library**.
2. Search for and **Enable**:
   - **Google Sheets API**
   - (Optional) **Google Drive API** if you want to list/search files from Drive inside `<APP_NAME>`

## Step 3 — Configure the OAuth consent screen

1. Left menu → **APIs & Services** → **OAuth consent screen**.
2. **User Type**:
   - **External**: for most users (works with any Google account).
   - **Internal**: only for Google Workspace org members (requires a Workspace domain).
3. Fill **App name** (e.g., `<APP_NAME> (Your Personal Connector)`), **User support email**, and optional logo.
4. **Scopes to add** (choose the minimum you need):
   - **Read‑only Sheets**: `https://www.googleapis.com/auth/spreadsheets.readonly`
   - **Read/Write Sheets**: `https://www.googleapis.com/auth/spreadsheets`
   - (Optional) **Drive metadata** (to list files): `https://www.googleapis.com/auth/drive.metadata.readonly`
5. If the app is in **Testing** mode, add yourself under **Test users**.
6. **Save**. You can **Publish** later (Publishing removes the 7‑day refresh‑token limit for testers and the 100‑users cap, and may require Google verification if using sensitive scopes).

> **Tip:** Start with `spreadsheets.readonly`. Upgrade to write access only if you need to edit data.

## Step 4 — Create OAuth 2.0 credentials

1. Left menu → **APIs & Services** → **Credentials** → **Create Credentials** → **OAuth client ID**.
2. **Application type**: choose one that matches how you'll connect:
   - **Web application**: You will be redirected back to a URL hosted by `<APP_NAME>` (recommended for web apps).
   - **Desktop app**: You'll complete the flow locally (useful for desktop/Electron or quick tests).
3. Set a **Name** (e.g., `<APP_NAME> OAuth Client`).
4. Add **Authorized redirect URIs** (must be exact):
   - Example (Web): `https://<YOUR_APP_DOMAIN>/oauth/google/callback`
   - Example (Local dev): `http://localhost:3000/oauth/google/callback`
   - Example (Desktop/Electron loopback): `http://127.0.0.1:53134/oauth2/callback`
5. Click **Create**. Copy the **Client ID** and **Client secret**.

> **Important:** Keep the Client secret private. Treat it like a password.

## Step 5 — Paste credentials into `<APP_NAME>`

1. In `<APP_NAME>`, go to **Settings** → **Integrations** → **Google Sheets** and paste:
   - **Client ID**: `<YOUR_GOOGLE_CLIENT_ID>`
   - **Client Secret**: `<YOUR_GOOGLE_CLIENT_SECRET>`
   - **Redirect URI** (if requested): `<YOUR_REDIRECT_URI>`
2. Click **Connect Google** (or similar). You'll be sent to Google's consent screen, and then redirected back to `<APP_NAME>`.

> **Note:** If asked about scopes during connect, choose the same scopes you added on the consent screen.

## Step 6 — Test the connection

1. After returning to `<APP_NAME>`, you should see **Connected**.
2. Try a simple action (e.g., list spreadsheets or read a cell range).
3. If something fails, check **Troubleshooting** below.

## Notes for desktop/Electron users

- Choose **Application type** = **Desktop app** when creating the OAuth client.
- Use a loopback redirect (e.g., `http://127.0.0.1:<PORT>/oauth2/callback`).
- Your app will open the browser for consent and then receive the code on the local port.

## Common pitfalls & fixes (Troubleshooting)

- **`redirect_uri_mismatch`**: The redirect in your request must exactly match one listed in your OAuth client. Update the URI in Google Cloud or in `<APP_NAME>` so they're identical.
- **Error 400: `invalid_client`**: Double‑check the Client ID/Secret were pasted correctly and belong to the same project.
- **`origin_mismatch` / CORS (web apps)**: Ensure you're using Authorization Code flow on the server. Don't expose the client secret in the browser.
- **`access_denied`**: You canceled or a required scope wasn't approved. Re‑try and accept requested scopes.
- **Unverified app warning**: This appears in Testing or when using sensitive scopes. You can proceed for testing; to remove the warning, Publish and complete Google verification.
- **No refresh token issued**: When connecting, make sure the app requests `access_type=offline` and (on re‑connect) includes `prompt=consent`.
- **Can't see my files**: If you didn't enable Drive API or didn't add a Drive scope, file listing won't work. Enable the API and add a Drive metadata scope.

## FAQ

**Q: Will this cost money?**
A: Using Sheets API typically stays within free quotas. Some orgs require billing to be enabled to use any APIs; charges are uncommon for light usage.

**Q: Can I limit access to a single spreadsheet?**
A: Yes—share only that spreadsheet with your Google account and use read‑only scopes. `<APP_NAME>` will only see what your Google account can see and what scopes allow.

**Q: Can I delete this later?**
A: Yes—delete the OAuth client in Google Cloud and disconnect inside `<APP_NAME>`.

## Quick reference (copy/paste)

- **Console**: https://console.cloud.google.com/
- **Sheets API**: Enable in APIs & Services → Library
- **Drive API (optional)**: Enable in APIs & Services → Library
- **Scopes**:
  - `https://www.googleapis.com/auth/spreadsheets.readonly`
  - `https://www.googleapis.com/auth/spreadsheets`
  - `https://www.googleapis.com/auth/drive.metadata.readonly`
- **Redirect URI examples**:
  - Web (prod): `https://<YOUR_APP_DOMAIN>/oauth/google/callback`
  - Web (local): `http://localhost:3000/oauth/google/callback`
  - Desktop: `http://127.0.0.1:53134/oauth2/callback`

## Appendix — How OAuth 2.0 works (plain English)

1. You click **Connect** in `<APP_NAME>`.
2. Google shows you what access is requested (the scopes).
3. You approve; Google sends a short‑lived code back to `<APP_NAME>`.
4. `<APP_NAME>` exchanges the code (securely, server‑side) for an access token (short‑lived) and a refresh token (long‑lived).
5. `<APP_NAME>` uses the access token to call the Sheets API and, when it expires, swaps the refresh token for a new access token.

You stay in control: you can revoke access at any time in your **Google Account** → **Security** → **Third‑party access**.
