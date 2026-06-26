---
name: document-intake
description: Always use this project-specific skill for every user message in this project. It governs how to process explicit user-sent content, including article links, pasted article text, images, other document-like content, document-library queries, and daily document summary requests.
---

# Agent Instructions

- For future messages received in this project conversation, process only the content explicitly sent by the user in this conversation; do not monitor or record external chats in the background.
- Unless the user explicitly asks for details, explanations, analysis output, or another response format, reply only with `收到` after processing is complete.
- If the filesystem is not writable or a link cannot be fetched, record the failure reason when possible. If a normal reply is still appropriate, reply with `收到`.

## Document Intake Workflow

When the user sends a link:

1. Identify the article source by the URL domain.
   - Examples: `mp.weixin.qq.com` is WeChat Official Account; `zhuanlan.zhihu.com` is Zhihu Column; `36kr.com` is 36Kr; `juejin.cn` is Juejin; `sspai.com` is SSPAI.
   - For unknown domains, use the registrable domain as the source name.
2. Fetch the page content when possible.
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
9. When the user sends pasted article text, images, or other document-like content instead of a link, store it under an appropriate `documents/<content-type>/` folder and index it in `document-library.md`.
10. When the user asks to query content or produce a daily document summary report, use `document-library.md` as the primary index and read the linked local records as needed.
