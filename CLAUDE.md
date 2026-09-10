# Project Learning Log

For every project, maintain a file named FOR_HUMAN.md as a living learning log that is updated continuously 
as work progresses. It should briefly explain what was built and why, how the system is structured and 
how its parts connect, and the key technical decisions along with their trade-offs. 
It should also capture important bugs, their root causes, and how they were fixed, 
as well as any pitfalls, useful patterns, and best practices discovered along the way.

Include a short reflection on how the AI was used, what worked well or poorly, and how prompts or 
approaches could be improved in future work. Keep the document concise and focused on insights 
that would genuinely help future you, and update it after meaningful changes such as new 
features, major decisions, or resolved issues.

# Text File Encoding

All text files in this repository MUST contain 7-bit ASCII only (bytes 0x00-0x7F).
This applies to source code, Markdown, configuration files, scripts, documentation, and other text-based files.

- Do not introduce Unicode or other non-ASCII characters.
- Use plain ASCII equivalents for punctuation and symbols.
- Use `-` instead of en/em dashes, straight quotes instead of curly quotes, and `...` instead of the ellipsis character.
- Do not use emoji or decorative Unicode characters.
- Before creating or modifying a text file, ensure all added content is 7-bit ASCII.

# Markdown Style

When producing Markdown, use clean, portable Markdown that remains easy to read, edit, and reorder. 
Do not use horizontal rules or document delimiters such as `---`, and do not number headings. 
Use unnumbered headings (`#`, `##`, `###`, etc.) sparingly; avoid deep or fragmented hierarchies 
and sections containing only a heading and a single sentence. Prefer fewer, well-populated 
sections with coherent paragraphs. Use bullet lists and code blocks where appropriate. 
Keep explanations dense and avoid unnecessary repetition. Avoid renderer-specific Markdown 
extensions, except Mermaid. Use Mermaid diagrams where they improve illustration or clarification.
