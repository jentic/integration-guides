# Customer Guide: Connect Your App to Discord

This guide walks you through setting up Discord integration for your app. Discord offers two main integration approaches: **OAuth 2.0** for user authentication and **Bot tokens** for automated interactions.

Replace items in `<ANGLE_BRACKETS>` with your app's details.

## Who this is for

Anyone who wants `<APP_NAME>` to integrate with Discord for user authentication, server management, or automated messaging.

## Integration Options

Choose the approach that fits your needs:

- **OAuth 2.0**: Authenticate users and access their Discord data (servers, profile, etc.)
- **Bot Token**: Create a bot that can join servers and perform automated actions
- **Both**: Use OAuth for user auth and Bot for automated features

## Prerequisites

- A Discord account
- `<APP_NAME>` admin or integration settings access
- Basic understanding of your app's redirect URL structure

## Step 1 — Create a Discord Application

1. Go to https://discord.com/developers/applications
2. Click **New Application**
3. Enter an **Application Name** (e.g., `<APP_NAME> Integration`)
4. Click **Create**
5. Note your **Application ID** (you'll need this later)

## Step 2A — OAuth 2.0 Setup (For User Authentication)

### Configure OAuth Settings

1. In your Discord application, go to **OAuth2** → **General**
2. Add **Redirect URIs**:
   - Production: `https://<YOUR_APP_DOMAIN>/oauth/discord/callback`
   - Development: `http://localhost:3000/oauth/discord/callback`
3. Copy your **Client ID** and **Client Secret**

### Set OAuth Scopes

Common scopes for user authentication:
- `identify` - Basic user info (username, avatar, etc.)
- `email` - User's email address
- `guilds` - List of servers user is in
- `guilds.join` - Join users to servers
- `connections` - User's connected accounts

### OAuth Flow URLs

**Authorization URL:**
```
https://discord.com/api/oauth2/authorize?client_id=<CLIENT_ID>&redirect_uri=<REDIRECT_URI>&response_type=code&scope=<SCOPES>
```

**Token Exchange URL:**
```
POST https://discord.com/api/oauth2/token
```

## Step 2B — Bot Setup (For Automated Actions)

### Create a Bot

1. In your Discord application, go to **Bot**
2. Click **Add Bot** → **Yes, do it!**
3. Copy the **Bot Token** (keep this secret!)
4. Configure bot permissions:
   - **Send Messages** - Basic messaging
   - **Read Message History** - Access chat history
   - **Manage Messages** - Delete/edit messages
   - **Add Reactions** - React to messages
   - **View Channels** - See channel list

### Bot Invite URL

Generate an invite link:
```
https://discord.com/api/oauth2/authorize?client_id=<APPLICATION_ID>&permissions=<PERMISSION_INTEGER>&scope=bot
```

Replace `<PERMISSION_INTEGER>` with the calculated permissions value from the Bot section.

## Step 3 — Configure `<APP_NAME>`

### For OAuth Integration

In `<APP_NAME>`, go to **Settings** → **Integrations** → **Discord** and enter:

- **Client ID**: `<YOUR_DISCORD_CLIENT_ID>`
- **Client Secret**: `<YOUR_DISCORD_CLIENT_SECRET>`
- **Redirect URI**: `<YOUR_REDIRECT_URI>`
- **Scopes**: Select the scopes you configured

### For Bot Integration

In `<APP_NAME>`, go to **Settings** → **Integrations** → **Discord Bot** and enter:

- **Bot Token**: `<YOUR_BOT_TOKEN>`
- **Application ID**: `<YOUR_APPLICATION_ID>`

## Step 4 — Test the Integration

### Testing OAuth

1. Click **Connect Discord** in `<APP_NAME>`
2. You'll be redirected to Discord's authorization page
3. Approve the requested permissions
4. You should be redirected back to `<APP_NAME>` with a success message

### Testing Bot

1. Use the bot invite URL to add your bot to a test server
2. In `<APP_NAME>`, try sending a test message or performing a bot action
3. Verify the bot responds correctly in your Discord server

## Common Use Cases

### OAuth 2.0 Use Cases
- **User Login**: "Sign in with Discord"
- **Server Verification**: Check if user is in specific servers
- **Profile Integration**: Display Discord avatar/username
- **Server Management**: List user's servers for configuration

### Bot Use Cases
- **Notifications**: Send alerts to Discord channels
- **Moderation**: Auto-moderate chat messages
- **Commands**: Respond to slash commands
- **Webhooks**: Post updates from your app

## API Endpoints Reference

### User Info (OAuth)
```
GET https://discord.com/api/users/@me
Authorization: Bearer <ACCESS_TOKEN>
```

### User Guilds (OAuth)
```
GET https://discord.com/api/users/@me/guilds
Authorization: Bearer <ACCESS_TOKEN>
```

### Send Message (Bot)
```
POST https://discord.com/api/channels/<CHANNEL_ID>/messages
Authorization: Bot <BOT_TOKEN>
Content-Type: application/json

{
  "content": "Hello from <APP_NAME>!"
}
```

## Troubleshooting

### OAuth Issues

- **`invalid_client`**: Check Client ID/Secret are correct and from the same application
- **`redirect_uri_mismatch`**: Ensure redirect URI in Discord matches exactly what your app sends
- **`access_denied`**: User canceled authorization or required scope wasn't approved
- **`invalid_scope`**: Check that all requested scopes are valid Discord scopes

### Bot Issues

- **`Unauthorized`**: Verify bot token is correct and hasn't been regenerated
- **`Missing Permissions`**: Bot needs appropriate permissions in the target server
- **`Cannot send messages`**: Check bot has `Send Messages` permission in the channel
- **Bot not responding**: Ensure bot is online and properly configured

### Rate Limiting

Discord has rate limits:
- **Global**: 50 requests per second
- **Per Route**: Varies by endpoint
- **Bot Messages**: 5 messages per 5 seconds per channel

Handle rate limits by implementing exponential backoff and respecting `X-RateLimit-*` headers.

## Security Best Practices

1. **Never expose Bot Token**: Keep it server-side only
2. **Validate OAuth state**: Use CSRF protection in OAuth flow
3. **Scope Minimization**: Only request necessary permissions
4. **Token Storage**: Store tokens securely (encrypted at rest)
5. **Regular Rotation**: Regenerate tokens periodically

## FAQ

**Q: Can I use both OAuth and Bot in the same app?**
A: Yes! Many apps use OAuth for user authentication and Bot for automated features.

**Q: Do I need Discord Nitro or a paid plan?**
A: No, Discord's API is free to use with rate limits.

**Q: Can my bot join multiple servers?**
A: Yes, but each server owner must invite your bot using the invite URL.

**Q: How do I handle Discord server verification levels?**
A: Higher verification levels may require phone verification for your Discord account.

## Quick Reference

- **Developer Portal**: https://discord.com/developers/applications
- **API Documentation**: https://discord.com/developers/docs
- **OAuth Scopes**: `identify`, `email`, `guilds`, `guilds.join`, `connections`
- **Bot Permissions Calculator**: Available in the Bot section of your application
- **Rate Limits**: 50 req/sec global, varies per endpoint

## Webhook Alternative

For simple notifications, consider Discord Webhooks (no OAuth/Bot needed):

1. In your Discord server: **Server Settings** → **Integrations** → **Webhooks**
2. Create webhook and copy URL
3. POST messages directly to webhook URL:

```bash
curl -X POST "<WEBHOOK_URL>" \
  -H "Content-Type: application/json" \
  -d '{"content": "Hello from <APP_NAME>!"}'
```

This is perfect for simple notifications without full Discord integration.
