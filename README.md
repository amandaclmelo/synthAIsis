# SynthAIsis — Legal Document AI Summarizer

An n8n automation workflow that summarizes legal documents using Claude AI. Drop a PDF into a folder and receive a structured summary by email in under 60 seconds.

## What it does

1. Detects a new PDF in a monitored folder
2. Extracts the text from the document
3. Sends it to Claude (Anthropic API) for analysis
4. Saves a structured summary as a file
5. Sends an email with the link to the summary

## Supported document types

The prompt is adaptive — Claude identifies the document type and chooses appropriate section titles for each one: contracts, arbitral awards, court decisions, NDAs, powers of attorney, and other legal documents.

## Two deployment architectures

### Cloud version (proof of concept)

```
[Google Drive Trigger]
        ↓
[Google Drive — Download File]
        ↓
[Extract From File]
        ↓
[HTTP Request — Claude API]
        ↓
[Google Docs — Create + Append]
        ↓
[Google Drive — Move to Summaries]
        ↓
[Gmail — Send link]
```

Stack: n8n.cloud + Google Drive + Google Docs + Anthropic API + Gmail

### Self-hosted version (production-ready for law firms)

```
[Local File Trigger]
        ↓
[Extract From File]
        ↓
[HTTP Request — Claude API]
        ↓
[Write File — local .txt or .md]
        ↓
[Send Email — SMTP]
```

Stack: n8n self-hosted + local filesystem + Anthropic API + firm's SMTP server

The self-hosted version eliminates all third-party cloud storage. Documents never leave the firm's infrastructure except for the API call to Anthropic.

## Stack

| Component | Cloud version | Self-hosted version |
|---|---|---|
| Workflow orchestration | n8n.cloud | n8n self-hosted |
| Document source | Google Drive | Local filesystem |
| Text extraction | n8n Extract From File | n8n Extract From File |
| AI / LLM | Claude claude-opus-4-6 (Anthropic API) | Claude API or self-hosted LLM |
| Output storage | Google Docs | Local file (.txt / .md) |
| Notification | Gmail | SMTP (firm's mail server) |

## How to use (cloud version)

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

Plain text with CAPS section titles, optimized for readability. Claude adapts the structure to the document type — a contract summary looks different from an arbitral award summary.

---

## Compliance & Data Privacy

This section addresses the specific requirements of legal professionals operating under attorney-client privilege obligations (French *secret professionnel*, Art. 66-5, Loi du 31 décembre 1971).

### The Anthropic API compliance layer

When using the Anthropic API (not Claude.ai consumer products), the following protections apply by default under Anthropic's standard commercial terms:

- **No model training on customer data** — explicitly prohibited in Anthropic's API Terms of Service: *"Anthropic may not train models on Customer Content from Services."*
- **No data retention by default** — prompts and responses are not stored after the API response is returned. Anthropic's official documentation states: *"Conversation content (your prompts and Claude's outputs) is not retained by default."*
- **DPA included** — Anthropic's commercial terms incorporate a Data Processing Addendum compliant with GDPR Art. 28. The API customer is the data controller; Anthropic is the data processor.
- **SCCs for EU→US transfers** — Standard Contractual Clauses (Module 2, controller to processor) are incorporated by reference in Anthropic's DPA, providing the legal basis for cross-border data transfers under GDPR.
- **Zero Data Retention (ZDR)** available on request for organizations requiring contractual guarantees — the Messages API and PDF support are both ZDR-eligible.

The Anthropic API therefore operates under what French legal tech practitioners classify as the *régime souverain via API* — the most protective framework for handling data subject to professional secrecy, as described in analysis published by Magnum Legal Club (April 2026).

### Remaining considerations by architecture

| Risk | Cloud version | Self-hosted version |
|---|---|---|
| Anthropic API data retention | None by default ✓ | None by default ✓ |
| Anthropic training on data | Prohibited by contract ✓ | Prohibited by contract ✓ |
| EU→US transfer legal basis | SCCs incorporated ✓ | SCCs incorporated ✓ |
| Google Drive/Docs data transit | Requires Google Workspace DPA | Eliminated ✓ |
| n8n.cloud data transit | n8n DPA available (EU-hosted) | Eliminated ✓ |
| Gmail data transit | Requires Google Workspace DPA | Eliminated ✓ |

### On pseudonymization

The CNB's March 2026 guide on AI and professional ethics recommends pseudonymizing client data before submitting to generative AI tools. This requirement applies specifically to **consumer-tier AI products** (Claude.ai Free/Pro, ChatGPT Free/Plus), where no DPA exists and data may be used for model training.

With enterprise-grade API access — which carries a DPA, zero retention, and an explicit prohibition on training — the AI provider becomes a data processor under GDPR Art. 28, functionally similar to engaging an expert or translator under confidentiality obligations. Pseudonymization remains a recommended additional safeguard for sensitive matters but is not the sole mechanism for compliance.

This distinction is confirmed by practice: major French law firms including Bredin Prat and CMS Francis Lefebvre adopted Harvey AI in May 2026 under enterprise contractual frameworks, without requiring pseudonymization of all client documents.

### What remains the lawyer's responsibility

Regardless of technical architecture, the following obligations rest with the lawyer and cannot be delegated to any tool:

- Informing clients that AI tools are used in handling their matter (recommended by CNB; may be included in the *convention d'honoraires*)
- Maintaining active, critical oversight of all AI outputs
- Systematically verifying any legal references before use in professional documents
- Defining which matter types are appropriate for AI-assisted processing
- Maintaining an internal policy (*charte interne*) documenting authorized tools, procedures, and responsibilities

### Production deployment checklist

For law firms deploying this workflow with real client data:

- [ ] Confirm Anthropic API account (not Claude.ai consumer) — DPA and no-training terms apply
- [ ] Self-hosted n8n or verify n8n DPA for n8n.cloud
- [ ] Google Workspace Business/Enterprise with DPA activated, if using cloud version
- [ ] Update firm's GDPR processing register (*registre des traitements*)
- [ ] Add AI usage clause to *convention d'honoraires*
- [ ] Train all staff (lawyers and administrative) on the tool and its limits
- [ ] Define matter types eligible for AI-assisted processing
- [ ] Implement output verification protocol

A compliance-ready deployment of this workflow is available as a consulting engagement.

---

Built as part of a legal AI consultancy portfolio. EN/FR/ES/PT markets.
