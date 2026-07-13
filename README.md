# SynthAIsis — Legal Document AI Summarizer

An n8n automation workflow that summarizes legal documents using Claude AI. Drop a PDF into a Google Drive folder and receive a structured summary by email in under 60 seconds.

## What it does

1. Detects a new PDF in a Google Drive folder
2. Extracts the text from the document
3. Sends it to Claude (Anthropic API) for analysis
4. Creates a Google Doc with the structured summary
5. Moves the Doc to a Summaries subfolder
6. Sends an email with the link to the summary

## Supported document types

The prompt is adaptive — Claude identifies the document type and chooses appropriate section titles for each one:

- Contracts
- Arbitral awards
- Court decisions
- NDAs
- Powers of attorney
- And other legal documents

## Stack

| Component | Tool |
|---|---|
| Workflow orchestration | n8n |
| Document source | Google Drive |
| Text extraction | n8n Extract From File |
| AI / LLM | Claude claude-opus-4-6 (Anthropic API) |
| Output storage | Google Docs |
| Notification | Gmail |

## How to use

1. Import the `synthaisis-workflow.json` file into your n8n instance
2. Set up credentials: Google (Drive + Docs + Gmail) and Anthropic API
3. Replace `YOUR_ANTHROPIC_API_KEY` in the HTTP Request node with your key
4. Set your Google Drive trigger folder
5. Drop a PDF into the folder and watch it run

## Known limitations

- Does not work with scanned PDFs (image-based) — would require OCR
- Documents over ~50 pages may need chunking for best results
- Requires active internet connection and API credits

## Output format

Plain text with CAPS section titles, optimized for readability inside Google Docs. Claude adapts the structure to the document type — a contract summary looks different from an arbitral award summary.
