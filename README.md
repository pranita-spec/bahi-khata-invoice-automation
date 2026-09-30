# Bahi-Khata — AI Invoice AP Automation

An end-to-end accounts payable pipeline that turns incoming invoice PDFs into posted bills in Zoho Books, with AI extraction and automatic general ledger (GL) account matching.

**Built with:** n8n · Claude API · OpenAI Embeddings · Supabase (pgvector) · Zoho Books API · React · Gmail

---
<img width="1093" height="157" alt="01_BahiKhata_ss" src="https://github.com/user-attachments/assets/bd526064-2956-4f3a-b5d7-2dd0b387c6f7" />
[bahi khata workflow.json](https://github.com/user-attachments/files/32845875/bahi.khata.workflow.json)

## The Problem
Small businesses and accounting teams spend hours manually reading invoices, typing line items into accounting software, and deciding which GL account each expense belongs to. It's slow, repetitive, and error-prone.

## What It Does
1. **Capture:** Invoices arrive via a React upload interface or a Gmail trigger.
2. **Extract:** Claude reads the PDF and returns structured data (vendor, invoice number, dates, line items, amounts, tax).
3. **Deduplicate:** A Supabase check prevents the same invoice from being processed twice.
4. **Match GL accounts:** Each line item is embedded (OpenAI) and matched to the most relevant GL account using pgvector similarity search.
5. **Post:** A bill is created in Zoho Books via OAuth-authenticated API calls.
6. **Review:** A results screen shows what was extracted and matched.

## Architecture
![Workflow diagram](diagram.png)

## Key Technical Challenges Solved
- Handling base64 PDF data correctly in n8n expressions for the Claude API
- Resolving ambiguous pgvector function overloads with explicit `vector(1536)` signatures
- Backfilling GL account embeddings for accurate similarity matching
- Zoho Books OAuth setup and India-edition field requirements (GST)
- Wiring n8n webhook paths to a React front end

## Demo
🎥 [Watch the Loom demo](YOUR-LOOM-LINK)

## Files
- `workflow.json` — n8n workflow export (credentials removed)
- `diagram.png` — architecture diagram

---
Built by **Pranita Priya**, n8n & AI Automation Builder
