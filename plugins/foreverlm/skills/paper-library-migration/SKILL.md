---
name: foreverlm-paper-library-migration
description: Move a Zotero or Paperpile paper library into ForeverLM once — read the reference manager's records and PDFs on the user's Mac, import each paper through ForeverLM's paper pipeline, and report what landed.
---

# ForeverLM paper library migration

ForeverLM has no Zotero or Paperpile plugin, on purpose. A paper becomes searchable, listenable, open to Chat and to review only once its PDF lives in the user's ForeverLM library, in their private iCloud container. So the move is a one-time import, not a sync. Read the reference manager; never modify it.

## Before importing

1. Call `get_mcp_status`. `import_local_pdf_as_paper` runs on the Mac, so an active Mac relay is required. Without one, say so and stop instead of guessing.
2. Agree the scope with the user: the whole library, or one collection, folder or label? Roughly how many papers? Every import runs the paper metadata and cleanup models, so import in batches of about 25 and report after each batch.
3. Locate the material on the user's Mac.
   - **Zotero.** The data directory (default `~/Zotero`; Zotero → Settings → Advanced → Files and Folders shows it) holds `zotero.sqlite` and `storage/<KEY>/<file>.pdf`. Zotero locks the database while it runs: copy `zotero.sqlite` to a temporary path and query the copy read-only. A BibTeX or RIS export (File → Export Library…, with "Export Files" checked) works too; its `file` fields point at the exported `files/` folder.
   - **Paperpile.** There is no local database. Export BibTeX from Paperpile (Export → BibTeX). PDFs live in the Google Drive folder `Paperpile/All Papers/…` when Drive sync is on, and the export's `file` field is relative to the `Paperpile` folder. Papers without a synced PDF are imported by DOI.

## Inventory first

Build a list of title, DOI and PDF path before touching ForeverLM, and show the user the count plus a few sample rows. In `zotero.sqlite`: `items` joined to `itemTypes` (keep journalArticle, conferencePaper, preprint, thesis, report, manuscript; skip notes, web pages and standalone attachments unless asked), `itemData` → `fields` → `itemDataValues` for `DOI`, `url` and `title`, `itemAttachments` for the PDF (`path` of the form `storage:Name.pdf` resolves to `storage/<attachment key>/Name.pdf`; linked files carry an absolute path), and `deletedItems` to exclude the trash.

## Import

- A PDF on disk: `import_local_pdf_as_paper` with the absolute path and the title as a hint. ForeverLM copies the file into the synced Papers folder, extracts the text, saves the paper with its PDF and answers at once with its id and a `work_id`; the metadata and cleanup models then read it in the background. It deduplicates on the DOI it finds in the PDF, so a paper already in the library takes the PDF instead of being added twice.
- A DOI or URL but no PDF: `import_paper_from_url` with `https://doi.org/<DOI>`. It answers once the PDF is downloaded, with the paper's id and a `work_id`, and the models read it in the background as they read a local PDF. A paper the publisher blocks is still added, marked paywalled, ready for the user to attach the PDF later.
- Neither: skip it and name it in the report.
- Send a batch's imports, local and by URL, then call `list_running_ai_work` until none of their `work_id`s is listed before reporting the batch: the background reading takes a minute or two a paper, a few papers at a time. A paper whose reading failed keeps its PDF and shows the failure; `extract_paper_metadata` retries it. Never delete, move or rename anything in the Zotero or Paperpile folders.

## Report

After the last batch, call `find_duplicate_papers` and relay what it finds. Give the counts of imported, already present, paywalled and skipped papers, with the skipped titles. The papers now sync through iCloud to the user's other devices and are ready for Text to Speech, search, Chat and review; Zotero or Paperpile stays untouched for citations.
