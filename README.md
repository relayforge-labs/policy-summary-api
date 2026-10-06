# Policy Summary API

A small Node.js client for turning plain-text policy documents into a first-pass summary. It sends a document to an OpenAI-compatible chat completions endpoint and prints the model response to stdout.

Use it when reviewing a revised internal policy, comparing obligations across documents, or preparing a summary for a human reviewer. This repository contains a command-line example; it does not run an HTTP server.

## Quick start

Requires Node.js 20 or newer. There are no third-party dependencies.

```sh
git clone https://github.com/relayforge-labs/policy-summary-api.git
cd policy-summary-api
npm start
```

The default command reads `examples/input.txt`. To summarize your own UTF-8 text file:

```sh
node index.mjs ./path/to/policy.txt > summary.txt
```

## Configuration

The client reads development settings from `config/local.env`. Environment variables take precedence over those values:

| Variable | Purpose |
| --- | --- |
| `OPENAI_BASE_URL` | Gateway API base URL, including `/v1` |
| `OPENAI_API_KEY` | Credential for the selected gateway |
| `OPENAI_MODEL` | Model route; defaults to `gpt-6-sol` |

```sh
OPENAI_BASE_URL=https://your-gateway.example/v1 \
OPENAI_API_KEY=your-credential \
OPENAI_MODEL=your-model-route \
node index.mjs ./policy.txt
```

The bundled configuration is for development. Use your own endpoint and credential for deployments, and keep deployment credentials out of Git. Model names are gateway route identifiers; their backing model depends on the gateway configuration.

## Request and output

Each run makes one `POST /chat/completions` request with a policy summarization instruction, the input document, and `temperature: 0`. The instruction asks for concise JSON for human review. The client prints the returned assistant content without parsing or validating it, so malformed JSON and incomplete summaries are possible.

When reviewing the result, check the policy's scope, required actions, exceptions, and any dates against the original document. Keep the source text alongside the summary. This example does not provide citations, document chunking, automatic retries, or a guaranteed output schema.

## Troubleshooting

- **File not found:** pass an existing text file, or run `npm start` from the repository directory.
- **`gateway returned 401`:** check the credential and endpoint configuration.
- **Other gateway errors:** the client exits with the HTTP status; inspect your gateway logs for details.
- **Input too large:** split the document before submitting it. Context limits depend on the selected gateway route.

## Data handling

The complete input document is sent to the configured gateway. Check that gateway's retention policy before sending confidential, personal, or regulated material. Use synthetic text for the bundled development example. Generated summaries require human review and are not legal advice.
