# Agent Instructions

- For future messages received in this project conversation, summarize and organize the content into the project directory when possible, then reply with `收到`.
- This applies only to messages explicitly sent in the current conversation; do not monitor or record external chats in the background.
- If the filesystem is not writable, explain that the content cannot be written.

## Document Intake Workflow

When the user sends a webpage link, article text, image, or other document-like content:

1. Store the content or a useful local summary under a categorized folder in the project directory.
2. Use clear top-level folders by content type, creating them when needed:
   - `documents/webpages/` for webpage links and webpage summaries.
   - `documents/articles/` for pasted articles or long-form text.
   - `documents/images/` for images or image descriptions.
   - `documents/files/` for other uploaded or referenced files.
3. Name stored items with a stable, sortable pattern:
   - `YYYY-MM-DD_sender_short-description.md` for text-based records.
   - `YYYY-MM-DD_sender_short-description.ext` for binary files when an actual file is available.
4. Keep a root-level document management table in `document-library.md`.
5. Create `document-library.md` if it does not exist.
6. For each intake item, append or update one row in `document-library.md` with these metadata fields:
   - Record ID
   - Sender
   - Intake time
   - Document type
   - Title or short description
   - Source URL or source note
   - Local storage path
   - Tags
   - Summary status
   - Notes
7. If sender, title, or other metadata is not explicit, infer conservatively and mark unknown values as `Unknown`.
8. Preserve the original source link when the user sends a URL. If browsing is not available or not needed, record the URL and summarize only what the user provided.
9. When the user asks to query content or produce a daily document summary report, use `document-library.md` as the primary index and read the linked local records as needed.
10. After successfully saving and indexing the received content, reply only with `收到` unless the user asks for details.
