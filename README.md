# Better Analyst PDF to Excel for Hermes Agent

[Better Analyst](https://betteranalyst.com) is an AI data analyst for spreadsheets, databases and business apps. This plugin gives Hermes Agent its [PDF to Excel converter](https://betteranalyst.com/pdf-to-excel-ai): ask for the rows you want from a bank statement, invoice, receipt, price list or report, and get back a preview and a downloadable `.xlsx`, one workbook per PDF.

## What the plugin contains

- **`mcp.json`**: connects to the hosted server at `https://app.betteranalyst.com/api/mcp/connector`.
- **`skills/pdf-to-excel`**: tells the agent how to collect the PDFs, name the columns, wait for the job and share the result.

There are no hooks, scripts or binaries in this package.

| Tool | What it does | Asks you first in Hermes |
|---|---|---|
| `create_pdf_upload_link` | Makes a private one-hour link where you upload PDFs from your device | Yes |
| `convert_pdf_to_excel` | Converts PDFs from an upload link or public HTTPS links, using your AI credits | Yes |
| `get_conversion_status` | Checks jobs and returns download links and preview rows | No (read-only) |
| `get_credit_balance` | Shows your remaining AI credits | No (read-only) |

## Install

You need Hermes Agent 0.21.6 or newer, Node.js 20.18.1 or newer with `npx` on your `PATH`, and a [Better Analyst account](https://betteranalyst.com). A free account includes 100 AI credits a month, enough for about 10 PDF pages.

```bash
hermes plugins install better-analyst
hermes plugins enable better-analyst
```

Start a new session. The first time the server starts, your browser opens the Better Analyst sign-in page. Sign in and approve. The token is kept for later sessions, so you only do this once. Sign-in needs a browser, so this plugin doesn't suit a headless machine.

## Try it

- "Convert this bank statement PDF to Excel with date, description and amount columns."
- "Turn the line items in https://example.com/invoice.pdf into a spreadsheet."
- "How many AI credits do I have left?"

Each PDF page uses 10 AI credits. See the [PDF to Excel docs](https://betteranalyst.com/docs/tools/pdf-to-excel) for supported documents and limits.

## Before you install

- **Sign-in bridge:** `mcp.json` runs the pinned `mcp-remote@0.15.2` through `npx`, which downloads it from npm on first use. It opens a local callback port for sign-in and stores the OAuth token in `~/.mcp-auth`. If nobody completes sign-in, it gives up after 55 seconds, including in cron and other unattended runs.
- **Network:** every tool call goes to `app.betteranalyst.com` and acts on your Better Analyst account. The PDFs you upload or link are sent to Better Analyst for processing, as described in the [privacy policy](https://betteranalyst.com/privacy-policy). The server decides the tool set at runtime, so the pinned commit doesn't cover it.
- **Spending:** conversions use your plan's AI credits. The package marks the server `untrusted`, so Hermes asks before every tool the server doesn't mark read-only, and refuses those tools in unattended runs.
- **Upload links:** anyone with a link can add PDFs to your conversion until it expires, so don't share it.
- **Usage records:** for each tool call the server records your account, the tool name, success or failure, the time taken and the calling client's user agent. It doesn't record file contents or tool arguments in that log. The package itself sends no telemetry.

## Support

Email [hello@betteranalyst.com](mailto:hello@betteranalyst.com). [Terms of service](https://betteranalyst.com/terms-of-services).

## License

The files in this repository are MIT licensed. The Better Analyst service is covered by its own terms.
