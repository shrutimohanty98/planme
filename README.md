# PlanMe

Mobile-friendly meal planning with a server-side recipe importer.

## Run locally

Requires Node 22+; there are no package dependencies.

```
npm run build
npm start
```

Open http://localhost:8765. Run `npm test` for importer tests.

## How imports work

Supported public recipe websites are fetched server-side and parsed for Recipe JSON-LD. No AI key is needed for this. Instagram links are not scraped: paste a recipe caption instead. Captions can be extracted with OpenAI only after a server-side key is configured. Every result is an editable draft; saving is explicit. Missing fields are never intentionally invented.

## AI configuration

Configure `OPENAI_API_KEY` and optionally `OPENAI_MODEL` (default `gpt-4o-mini`) as hosting runtime secrets, never in `web/`. `.env.example` lists names only. Locally, supply environment variables to Node, or use `node --env-file=.env scripts/dev.mjs` after creating an ignored `.env` file. No key is bundled. AI requests use the Responses API with structured output and `store:false`. Pasted text is sent to OpenAI only when caption extraction is requested.

## Hosting

The production build creates one Worker ESM at `dist/server/index.js` with all frontend assets embedded. The existing Sites project hosts this server with its private sign-in boundary. Static GitHub Pages cannot run the import API. For another host, deploy the Worker through a server-capable host, or adapt `scripts/dev.mjs` to that host's port and binding requirements. Keep the API same-origin.

## Files

- `web/`: editable frontend source, including `import.js` and `routine.js`
- `server/importer.mjs`: recipe parsing, allowed-host fetcher and optional AI extraction
- `server/importer.test.mjs`: parser and request safety tests
- `scripts/build.mjs`: embeds frontend and backend into Worker ESM
- `scripts/dev.mjs`: local Node adapter

## Current scope and limitations

- Nine home meals, three office lunches, two weekend meals out. Sunday has two batch dishes; Wednesday has one.
- Mystery recipes are sourced from a small pool. They stay hidden from the collection until explicitly saved. A saved mystery may replace the entire Wednesday batch so Friday dinner is part of that batch.
- Recipes/preferences remain in browser storage. Cross-device database sync is not implemented.
- No Instagram account access, private-post retrieval, video transcription, or screenshot import is implemented.
- Full publisher instructions open on their source sites. Imported drafts contain the extracted recipe data for personal review.
- Recipe photos remain the original publishers' content. Review third-party image permissions before wider distribution.
- Some publishers block server fetches or omit JSON-LD. Caption paste/manual entry is the fallback.
- The combined grocery quantity calculator is not implemented.
- AI is disabled until a server key is configured; its live provider call has not been verified without that key.
- Before making AI endpoints public, add authenticated per-user quotas and durable rate limiting. The current Site remains owner-private.

Future edits: modify `web/` or `server/`, run tests and build, commit and deploy. This repository is portable and contains no credentials. The hosting manifest records the existing Site identity; omit it when configuring unrelated hosting.
