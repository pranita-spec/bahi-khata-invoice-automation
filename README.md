# Bahi-Khata — AI Invoice AP Automation

An end-to-end accounts payable pipeline that turns incoming invoice PDFs into bills in Zoho Books. Claude extracts and classifies each invoice, a decision engine checks confidence and data quality, and anything uncertain goes to a human review queue before it's posted.

**Built with:** n8n · Claude API · OpenAI Embeddings · Supabase (pgvector) · Zoho Books API · React · Gmail

---

## The Problem
Small businesses and accounting teams spend hours reading invoices, typing line items into accounting software, and deciding which GL account each expense belongs to. It's slow and error-prone, and fully automating it is risky when the AI isn't sure.

## What It Does

### Phase 1: Intake, extraction and decision
1. **Capture:** A Gmail trigger picks up invoice emails and keeps only PDF attachments. Invoices can also be uploaded through a React interface via webhook.
2. **Extract:** Claude reads the PDF and returns structured data: vendor, invoice number, dates, line items, amounts and tax.
3. **Normalize and deduplicate:** The data is cleaned and validated, and a Supabase check stops the same invoice from being processed twice.
4. **Classify:** Claude assigns the expense to one of 13 GL categories and returns a confidence score with a reason.
5. **GL lookup:** OpenAI embeddings and pgvector similarity search match the category to the chart of accounts.
6. **Decision engine:** Invoices with **at least 85% confidence and all required fields** are processed automatically. Everything else goes to review.
   - **Auto:** the vendor is found or created in Zoho Books, and the bill is created.
   - **Review:** the invoice is stored in a Supabase review queue and the pipeline pauses.

### Phase 2: Human review
7. **Review API:** A webhook-based API serves the React review screen: list the pending queue, open an invoice, and approve or reject it.

### Phase 3: Resume after approval
8. **Post approved invoices:** When an invoice is approved, the resume workflow fetches it, matches the GL account, finds or creates the vendor in Zoho Books, and creates the bill.

## Architecture
![Workflow diagram](diagram.png)

## Key Technical Challenges Solved
- Handling base64 PDF data correctly in n8n expressions for the Claude API
- Parsing and validating Claude's JSON output, including confidence clamping and failure handling
- Resolving ambiguous pgvector function overloads with explicit `vector(1536)` signatures
- Backfilling GL account embeddings for accurate similarity matching
- Zoho Books OAuth token refresh and India-edition field requirements (GST)
- Pausing and resuming a pipeline around a human decision, across separate workflows

## Roadmap
- Map the matched GL account directly onto each Zoho bill line item (currently a default expense account is used)

## Files
- `bahikhata_phase1.json` — intake, extraction, classification and decision engine
- `bahikhata_human_review.json` — review queue API used by the React review screen
- `bahikhata_resume.json` — posts approved invoices to Zoho Books
- `diagram.png` — architecture diagram

All credentials, tokens and webhook paths are replaced with placeholders.

---
Built by **Pranita Priya**, n8n & AI Automation Builder
