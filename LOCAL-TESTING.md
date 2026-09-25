# Local testing (not part of the submitted package)

`@plugin-creator` also generates a `.app.json` and an `apps` pointer to it:

```json
{ "apps": { "pathtoship": { "id": "asdk_app_...", "required": true } } }
```

That `asdk_app_...` value is a **developer-mode registration that exists only in
the account that created it**. It is how a local plugin is wired to an MCP
connection you already registered in ChatGPT, so it is genuinely useful for
installing this plugin from a personal marketplace and testing it in a chat.

**Do not ship it in the submitted archive.** A public installer cannot resolve
another account's registration, and the submission portal takes the MCP server
by URL under *With MCP* and scans it directly — so the mapping is redundant
there as well as broken.

To test locally:

1. Register the server in ChatGPT developer mode at `chatgpt.com/plugins` and
   copy the `asdk_app_...` id from the URL.
2. Add `.app.json` with that id, and `"apps": "./.app.json"` inside
   `extensions.com.openai` in `plugin.json`.
3. Add a personal marketplace entry at `~/.agents/plugins/marketplace.json`
   pointing at this folder, restart the ChatGPT desktop app, and install from
   your local source.
4. **Remove both before building the submission archive.**
