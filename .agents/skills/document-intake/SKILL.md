---
name: document-intake
description: Mandatory project skill for every user message in this project, with no optional bypass. Use for any message the user sends, including links, plain text, pasted articles, images, document-like content, document-library queries, daily summary requests, and operational instructions, so user-sent information is fetched, evaluated, saved, indexed, or answered according to the intake rules.
---

# Agent Instructions

- This skill is mandatory for every user message in this project. Do not choose another workflow instead of this skill; apply this skill first, then perform any additional requested task if needed.
- For future messages received in this project conversation, process only the content explicitly sent by the user in this conversation; do not monitor or record external chats in the background.
- Unless the user explicitly asks for details, explanations, analysis output, or another response format, reply only with `收到` after processing is complete.
- Treat one user message as an intake batch. Process every link and every standalone text segment in that message before replying.
- Preserve user-sent wording. When saving text that contains effective information, save the original text verbatim; do not rewrite, summarize, translate, or normalize the content body unless the user explicitly asks for a transformed derivative in addition to the raw record.
- If the filesystem is not writable or a link cannot be fetched after reasonable attempts, record the failure reason when possible. If a normal reply is still appropriate, reply with `收到`.

## Document Intake Workflow

For every user message:

1. Identify all URLs, pasted article bodies, ordinary text, images, and explicit library/query instructions in the message.
2. Classify each item independently:
   - Link: any URL or URL-like text.
   - Text with effective information: any user-sent text that contains facts, requirements, decisions, procedures, summaries, opinions, observations, contact details, identifiers, task context, or reusable knowledge.
   - Non-informational text: acknowledgements, greetings, very short conversational fillers, or commands that contain no reusable information beyond the immediate interaction.
3. Save all links and all text with effective information to the local document library.
4. Do not save non-informational text unless it is part of a larger informative message.
5. After intake is complete, answer any explicit user request using the saved records and current workspace context as needed.

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
5. Keep a root-level document management table in `document-library.md`.
6. Create `document-library.md` if it does not exist.
7. For each saved article, append or update one row in `document-library.md` with these metadata fields:
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
8. If title, author, or other metadata is unavailable, infer conservatively and mark unknown values as `Unknown`.
9. When the user sends text instead of a link:
   - Analyze whether the text contains effective information using the classification rules above.
   - If it contains effective information, save the original user text verbatim under `documents/text/YYYY-MM-DD_short-description.md` and index it in `document-library.md`.
   - Use document type `User Text` unless a more specific type is obvious, such as `Pasted Article`, `Instruction`, `Note`, or `Conversation Context`.
   - If the message mixes an operational instruction with reusable information, save the reusable original text first, then carry out the operation.
10. When the user sends images or other document-like content instead of a link, store it under an appropriate `documents/<content-type>/` folder and index it in `document-library.md`.
11. When the user asks to query content or produce a daily document summary report, use `document-library.md` as the primary index and read the linked local records as needed.
