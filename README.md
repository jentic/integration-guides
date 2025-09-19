# Integration Guides for Jentic

Add your guide in Markdown using the following format:

`/vendor/api-name/guide.md` - for specific APIs within a vendor's ecosystem
or
`/vendor/guide.md` - for main/primary APIs or single-API vendors

Examples:

- `/googleapis.com/sheets/guide.md` - Google Sheets (specific API)
- `/googleapis.com/gmail/guide.md` - Gmail (specific API)
- `/discord.com/guide.md` - Discord (main API)
- `/stripe.com/guide.md` - Stripe (main API)

**Rule**: If the vendor only has one main API, place it directly at the vendor level (`/vendor/guide.md`). If the vendor has multiple distinct APIs, use the specific API name (`/vendor/api-name/guide.md`).

## Images

Include images in the same folder as your `guide.md` and reference them in Markdown.
