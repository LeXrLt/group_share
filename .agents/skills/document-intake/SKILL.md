---
name: document-intake
description: Mandatory project skill for every user message in this project, with no optional bypass. Use for any message the user sends, including plain text, low-information text, links, files, images, pasted articles, document-like content, document-library queries, wiki/knowledge-base maintenance requests, daily summary requests, operational instructions, group chat messages, direct messages, and mention-triggered messages, so user-sent information is classified, fetched, evaluated, saved as raw source, indexed, integrated into the maintained wiki, summarized, answered, ignored, or silently processed according to the intake, wiki, and reply rules.
---

# Agent Instructions

- Version: `1.0.1`
- Versioning policy: use Semantic Versioning for this skill. Increment PATCH for clarifications or non-behavioral wording, MINOR for backward-compatible workflow additions, and MAJOR for breaking changes to intake, storage, wiki, or reply behavior.
- When changing this skill, update the version in this section and add a concise entry under `## Version History`.
- Highest behavior rule: remain silent unless the user explicitly @mentions the bot or is chatting with the bot in a private/direct conversation. This rule overrides all other reply instructions in this skill and must never be violated.
- In group chats where the bot is not @mentioned, process and save eligible content normally, but send no reply after processing, including no success notice, no `收到`, no failure notice, no summary, and no task-completion notification.
- In private/direct conversations or @mentioned group messages, reply only when a reply is needed by the user's request or by the link/file summary rules below.
- User-facing replies should focus on the requested answer or concise content summary. Do not include routine intake bookkeeping such as local storage paths, `document-library.md` updates, wiki source paths, or "已入库/已保存" status unless the user explicitly asks where something was saved, asks for audit details, or a storage/fetch failure is the main result.
- This skill is mandatory for every user message in this project. Do not choose another workflow instead of this skill; apply this skill first, then perform any additional requested task if needed.
- For future messages received in this project conversation, process only the content explicitly sent by the user in this conversation; do not monitor or record external chats in the background.
- Treat one user message as an intake batch. Process every link and every standalone text segment in that message before replying.
- Preserve user-sent wording. When saving text that contains effective information, save the original text verbatim; do not rewrite, summarize, translate, or normalize the content body unless the user explicitly asks for a transformed derivative in addition to the raw record.
- If the filesystem is not writable, a link cannot be fetched, or a file cannot be read after reasonable attempts, record the failure reason when possible. Only notify the user when the highest behavior rule allows a reply.

## Knowledge Base Architecture

Maintain the project as a three-layer knowledge base:

1. Raw sources: `documents/` plus `document-library.md`.
   - Treat raw source records as immutable evidence once saved.
   - Store fetched articles, user text, files, image analyses, and access-failure records here.
2. Maintained wiki: `wiki/`.
   - Treat wiki pages as LLM-maintained synthesis artifacts.
   - Create and update summaries, concept pages, entity pages, reports, comparisons, and indexes as knowledge accumulates.
   - Use wiki links like `[[concepts/example]]` for cross-references.
3. Schema and workflow rules: this `SKILL.md`.
   - Keep this file as the operating contract for intake, wiki updates, query answering, linting, and reply behavior.

Use this default wiki layout:

- `wiki/home.md`: entry point and high-level orientation.
- `wiki/index.md`: content-oriented catalog of wiki pages with one-line descriptions.
- `wiki/log.md`: append-only chronological log. Start entries with `## [YYYY-MM-DD] operation | title`.
- `wiki/sources/`: one source-summary page per important ingested source.
- `wiki/concepts/`: reusable topic and concept pages.
- `wiki/entities/`: people, organizations, products, places, projects, and other named entities.
- `wiki/reports/`: durable analyses, comparisons, daily/weekly summaries, and query results worth preserving.

## Document Intake Workflow

For every user message:

1. First classify the message and each item in it as one or more of:
   - Plain text only.
   - Link: any URL or URL-like text.
   - File: any attached or referenced file with a filename or detectable type, such as PDF, DOCX, XLSX, TXT, ZIP, audio, or video.
   - Image: any attached or referenced image, such as PNG, JPG, JPEG, GIF, WebP, or HEIC.
   - Mixed content: any message combining text, links, files, or images.
2. For plain text only, analyze information value before doing any storage or response:
   - Text with effective information: any user-sent text that contains facts, requirements, decisions, procedures, summaries, opinions, observations, contact details, identifiers, task context, or reusable knowledge.
   - Low-information text: simple greetings, acknowledgements, meaningless text, standalone words or phrases, fillers, emojis, or text with no reusable information beyond the immediate interaction.
3. For low-information plain text, do nothing: do not save, do not index, and do not reply unless the user explicitly asked a meaningful question and the highest behavior rule allows a reply.
4. Save all links, all files/images, and all text with effective information to the local document library.
5. Do not save low-information text unless it is part of a larger informative mixed message.
6. For important or reusable sources, integrate the new record into `wiki/` after raw intake.
7. After intake is complete, answer any explicit user request using the saved records, `wiki/index.md`, and current workspace context as needed, subject to the highest behavior rule.

When the user sends a link:

1. Identify the article source by the URL domain.
   - Examples: `mp.weixin.qq.com` is WeChat Official Account; `zhuanlan.zhihu.com` is Zhihu Column; `36kr.com` is 36Kr; `juejin.cn` is Juejin; `sspai.com` is SSPAI.
   - For unknown domains, use the registrable domain as the source name.
2. Fetch the page content. A link is not sufficiently processed until the content pointed to by the link has been retrieved or a concrete retrieval failure has been recorded.
   - Use the available browser, shell, HTTP, or site-specific extraction methods needed for the domain.
   - For article pages, extract the readable article body rather than saving only metadata or the URL.
   - For WeChat Official Account links, attempt browser access and direct HTTP retrieval with a realistic user agent; extract title, publisher, description, and readable body from the returned HTML/JavaScript when possible.
3. Extract and save the article content as Markdown, including at least:
   - Title
   - Source domain and inferred source name
   - Original URL
   - Intake time
   - Main article body or the best available readable content
   - Notes about fetch or extraction limitations, if any
4. Save Markdown files under a source-specific folder:
   - `documents/<source-domain>/YYYY-MM-DD_short-title.md`
   - Normalize the domain and filename to lowercase ASCII where practical.
5. When the highest behavior rule allows a reply, reply with a concise summary of the article or linked content instead of simply saying `收到`.
   - Keep the summary brief and useful, usually 1-3 short bullets or 1 short paragraph.
   - If content could not be fetched, briefly state that the link was saved but the content could not be read, plus the concrete reason when available.
6. Keep a root-level document management table in `document-library.md`.
7. Create `document-library.md` if it does not exist.
8. For each saved article, append or update one row in `document-library.md` with these metadata fields:
   - Record ID
   - Sender
   - Intake time
   - Source domain
   - Source name
   - Document type
   - Title or short description
   - Original URL
   - Local storage path
   - Tags
   - Summary status
   - Notes
9. If title, author, or other metadata is unavailable, infer conservatively and mark unknown values as `Unknown`.
10. For important or reusable linked content, create or update `wiki/sources/YYYY-MM-DD_short-title.md` with a concise source summary, key ideas, source ID, source path, source URL, tags, and links to related wiki pages.
11. Update relevant `wiki/concepts/`, `wiki/entities/`, or `wiki/reports/` pages when the source changes or strengthens durable knowledge.
12. Update `wiki/index.md` and append an ingest entry to `wiki/log.md`.

When the user sends a file or image:

1. Detect the file type from the filename extension when available; otherwise infer from MIME type or content.
2. Save the original file or the best available extracted representation under an extension-specific folder:
   - `documents/<extension>/YYYY-MM-DD_short-description.<extension>` for original files.
   - `documents/<extension>/YYYY-MM-DD_short-description.md` for extracted text, OCR, metadata, or summaries.
   - Normalize extension names to lowercase without a leading dot, such as `pdf`, `png`, `jpg`, `docx`, `xlsx`, `txt`, or `unknown`.
3. Extract readable content when practical:
   - PDFs and office documents: extract text and key metadata.
   - Images: run OCR or visual inspection when available; record image dimensions, visible text, and salient content.
   - Other file types: extract metadata or a conservative description when full parsing is not available.
4. Index the file in `document-library.md` with document type `File`, `Image`, or a more specific type such as `PDF`, `PNG Image`, or `Spreadsheet`.
5. For important or reusable files/images, create or update a `wiki/sources/` page and any related concept/entity pages.
6. Update `wiki/index.md` and append an ingest entry to `wiki/log.md`.
7. When the highest behavior rule allows a reply, reply with a concise summary of the file content instead of simply saying `收到`.
   - If the file cannot be read, briefly state that it was saved or attempted, and include the concrete limitation when available.

When the user sends text instead of a link or file:

1. Analyze whether the text contains effective information using the classification rules above.
2. If it is low-information plain text, do nothing.
3. If it contains effective information:
   - Save the original user text verbatim under `documents/text/YYYY-MM-DD_short-description.md` and index it in `document-library.md`.
   - Use document type `User Text` unless a more specific type is obvious, such as `Pasted Article`, `Instruction`, `Note`, or `Conversation Context`.
   - If the message mixes an operational instruction with reusable information, save the reusable original text first, then carry out the operation.
4. When the highest behavior rule allows a reply and no link/file summary or explicit answer is required, keep the response minimal.

When the user asks to query content or produce a daily document summary report, read `wiki/index.md` first, then use `document-library.md` and linked local records as needed for source-level detail.

## Wiki Operations

When ingesting an important source:

1. Read the saved raw record from `documents/`.
2. Create or update a source summary page in `wiki/sources/`.
3. Identify durable concepts, entities, decisions, contradictions, and useful facts.
4. Update existing wiki pages before creating duplicate pages.
5. Create new concept/entity/report pages when the idea is likely to recur.
6. Add source IDs and local source paths in frontmatter or body references.
7. Update `wiki/index.md`.
8. Append an entry to `wiki/log.md`.

When answering a knowledge-base query:

1. Read `wiki/index.md` first.
2. Open the most relevant wiki pages.
3. Use `document-library.md` and raw `documents/` only when needed for evidence, missing detail, or source verification.
4. Answer from the maintained wiki when possible, with local file citations when useful.
5. If the answer creates reusable analysis, save it under `wiki/reports/` or merge it into an existing concept/entity page, then update `wiki/index.md` and `wiki/log.md`.

When producing daily or periodic summaries:

1. Filter `document-library.md` by intake date.
2. Read each local source record for that date.
3. Summarize by theme, not only by source order.
4. Optionally create a durable report under `wiki/reports/YYYY-MM-DD_daily-summary.md` when the summary has future value.
5. Update `wiki/index.md` and `wiki/log.md` if a report is created.

When linting the knowledge base:

1. Check `wiki/index.md` for missing or stale page listings.
2. Check `wiki/log.md` for recent operations and parseable headings.
3. Find orphan wiki pages with no obvious inbound links.
4. Look for contradictions, stale claims, duplicated concepts, missing source IDs, and uncited durable claims.
5. Suggest new sources or questions where gaps are clear.

## Version History

- `1.0.1` - Clarified user-facing reply style: omit routine storage, index, and wiki bookkeeping from normal summaries unless explicitly requested or needed for failure reporting.
- `1.0.0` - Baseline version for mandatory message intake, reply constraints, raw document storage, local indexing, file/image handling, and maintained wiki workflows.
