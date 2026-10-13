# Mosaic AI

A responsive browser chat assistant powered by your local Ollama model. Chat history stays in your browser; the Next.js server sends prompts to Ollama on your machine. Optional live web search is provided by Tavily and is only used for questions the server recognizes as time-sensitive or source-dependent.

## Requirements

- Node.js 20.9 or later
- [Ollama](https://ollama.com/) installed and running
- The `llama3.2:latest` model (or configure another Ollama model)
- Optional: a [Tavily API key](https://tavily.com/) for live web search

## Run locally

1. Confirm that Ollama is running and the model is available:

   ```powershell
   ollama list
   ollama run llama3.2:latest
   ```

   Ollama's local API should be reachable at `http://127.0.0.1:11434`.

2. Install dependencies and start the app:

   ```powershell
   npm install
   Copy-Item .env.example .env.local
   npm run dev
   ```

3. Open [http://localhost:3000](http://localhost:3000).

The app works without a Tavily key for general questions. To enable live search, add `TAVILY_API_KEY` to `.env.local` and restart the app. When a question needs current information but search is not configured or fails, the assistant explicitly says it could not verify the answer and shows no fabricated sources.

## Configuration

| Variable | Default | Purpose |
| --- | --- | --- |
| `OLLAMA_BASE_URL` | `http://127.0.0.1:11434` | Ollama server URL, read by the server only |
| `OLLAMA_MODEL` | `llama3.2:latest` | Model tag installed in Ollama |
| `TAVILY_API_KEY` | Unset | Optional server-side key for web search |

Keep `.env.local` private. API calls to Ollama and Tavily are made by the server; provider keys are never sent to the browser. Chats are stored in browser local storage and are not synchronized to an account or database.

## Production deployment

Build and run with:

```powershell
npm run build
npm run start
```

The deployment server must be able to reach the configured Ollama URL. A hosted deployment cannot use `127.0.0.1` to reach Ollama on your own computer; deploy the server on the same machine/network as Ollama and use a private, firewalled endpoint, or run the app locally. Set environment variables in the deployment platform rather than committing them. Add `TAVILY_API_KEY` if live search is required.

## Behavior and limits

- The app streams model output and supports conversation follow-ups, stop, regenerate, copy, new chats, and light/dark themes.
- Chat history is local to the browser. There is no account system or remote database.
- Search is triggered by a small server-side set of current-information indicators; it is not a general-purpose crawler. Search snippets are treated as untrusted evidence, and sources link to the URLs returned by Tavily.
- The server validates requests and limits each client address to 30 chat requests per minute. For a multi-instance deployment, use a shared rate limiter.
