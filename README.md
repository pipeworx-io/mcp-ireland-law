# @pipeworx/ireland-law

Irish Acts of the Oireachtas from the electronic Irish Statute Book, published
by the Office of the Attorney General: search Acts by title, then read an
Act's record, its list of sections and schedules, and the text of one
provision at a time. Citation (Act number and year) and enactment date come
with it.

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 2014+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

## Tools

- `ie_law_search(query, from_year?, to_year?)` — find Acts by words in the
  title. The Statute Book is read year by year, so a search covers a range:
  the last 25 years by default, or any range back to 1922 (for example
  `from_year` 1988, `to_year` 1988 for the Data Protection Act 1988). Newest
  first. Acts only, not statutory instruments.
- `ie_law_get(year, number, section?)` — one Act: title, citation, long
  title, enactment date, sections and schedules, and `section` for one
  provision's text (`"10"`, `"10A"`, `"schedule 2"`).

## Auth

Keyless.

## Data sources

- Ireland, Office of the Attorney General, electronic Irish Statute Book —
  Acts of the Oireachtas.
- Ireland, Law Reform Commission, Revised Acts — checked only to say whether a
  consolidated version exists and how current it is.

The Statute Book publishes Acts as enacted and does not state in-force status.
Text returned is as enacted, with later amendments not applied. `in_force` is
`true` only where a Revised Act exists (the Act is then in force at least in
part), and `null` (unknown, not false) otherwise; the Revised Act link is
returned for the consolidated text. `data_as_of` is the date of the live read.
Read live on each call; nothing is copied. A search or lookup that finds
nothing returns `empty_reason`, not an empty success.

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "ireland-law": {
      "url": "https://gateway.pipeworx.io/ireland-law/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/ireland-law/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 2014+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/ie_law_search \
  -H 'Content-Type: application/json' \
  -d '{"query":"data protection","from_year":1988,"to_year":2018}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/ie_law_search`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-ireland-law` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/ireland-law/mcp` endpoint shown above. It needs network access;
there is no offline mode.

```json
{
  "mcpServers": {
    "ireland-law": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-ireland-law"]
    }
  }
}
```

Without a key it calls anonymously, capped at 50 calls a day. A free account
raises that to 200 a day: get a key at https://pipeworx.io/signup and add
`"env": { "PIPEWORX_API_KEY": "<your key>" }` to the server entry.
The key is sent as a Bearer token, the same way a hosted connection sends it,
so calls are metered and billed exactly as they are there.

It lists **only** this pack's tools by default. Set `PIPEWORX_META_TOOLS=1` to
also list `ask_pipeworx` and the other gateway-wide tools.

Behind an HTTPS proxy: Node's built-in `fetch` does not read `HTTPS_PROXY` by
default. Newer Node releases honour it when `NODE_USE_ENV_PROXY=1` is set; check
your Node version's documentation.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Ireland Law data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
