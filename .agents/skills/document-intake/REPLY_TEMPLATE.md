# Document Intake Reply Template

Use this template for every user-facing reply that involves document intake, including links, files, images, pasted articles, and document-like content.

## Hard Rules

- Reply with the content summary only.
- Output only the text that would appear after labels such as `这条 X 的核心观点：`, `这个文件的核心内容：`, or `这张图片的核心内容：`.
- Do not include source/type labels, prefixes, colons, section names, or wrapper phrases.
- Do not describe intake, storage, indexing, wiki updates, local files, parsing, fetching, OCR, extraction, or any intermediate step.
- Do not say `已保存`, `已入库`, `已记录`, `已更新`, `已处理`, `收到`, or similar operational status text.
- Keep the reply to one short paragraph with at most two sentences, unless the user explicitly asks for a longer or structured answer.
- Do not add a heading unless the user explicitly asks for a structured format.

## Templates

For articles, webpages, and social posts:

```text
{用1到2句话概括内容本身。必要时第二句话只写关键前提、风险或注意点。}
```

Do not write prefixes like `这条 X 的核心观点：`, `这篇文章主要讲：`, or `这个链接的核心内容：`.

For files:

```text
{用1到2句话概括文件内容本身。必要时第二句话只写关键结论、风险或注意点。}
```

For images:

```text
{用1到2句话概括图片内容本身。必要时第二句话只写可见文字、关键细节或注意点。}
```

When content cannot be read:

```text
内容暂时无法总结：{只写内容层面的不可读原因。}
```
