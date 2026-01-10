# Prompt library *myprompt*

This repo **myprompt** is a general-purpose prompt library for working with AI systems.  
It contains both **small, reusable prompt snippets** and **larger, specification-style prompts** with accompanying documentation.

The goal of this repository is to treat prompts as **reusable interfaces**, not one-off instructions.

## How to use this repository

- Use inline prompt examples directly or adapt them to your needs.
- Read the Markdown documents for larger prompt specifications before using their corresponding `.txt` prompts.
- Treat the documented prompts as reference implementations rather than black boxes.

## Inline prompts

### Prompt - Markdown formatting rules

```
When producing Markdown output, follow these rules strictly:

- Do **not** use `---` or any other horizontal rule / document delimiter.
- Do **not** use numbered headings (for example `## 1. Title`, `### 2.1 Section`).
- Use unnumbered Markdown headings only (`#`, `##`, `###`, etc.).
- Structure the document using headings, paragraphs, bullet lists, and code blocks as appropriate.
- Preserve readability and allow for manual editing and reordering of sections.
- Ensure the Markdown renders correctly in common preview tools without relying on special extensions.

The goal is to produce clean, portable Markdown that is easy to read, edit, and maintain across different tools and environments.
```

### Prompt - Markdown style

```
When generating Markdown, avoid overly deep or fragmented hierarchies. Do not overuse headings, and avoid structures where sections contain only a heading and a single sentence. Prefer fewer, well-populated sections with coherent paragraphs over many short, repetitive sections.
```
```
Provide a dense explanation. Avoid repeating concepts already explained elsewhere and trust the reader to read subsequent sections.
```

### Prompt - File download, copy&paste

```
Produce the output as a downloadable file without modifying its content.
```
```
Open the output in a file or code editor view so it can be copied without reformatting.
```
```
Output as plain text exactly as generated, without markdown formatting, line wrapping, or additional explanation. Preserve all spaces and line breaks.
```

## More complex prompt specifications

In addition to small prompt snippets, the repository also contains **more complex prompts** that require formal definition and explanation.

### Prompt - Machine-Parseable Export of AI Conversation Context

A machine-parseable export of AI conversation context that enables safe reuse of AI context across systems.

- **[ai_context_export.md](./ai_context_export.md)**  
  A comprehensive Markdown document describing the design, terminology, and rationale behind exporting AI conversation context in a machine-parseable way.

- **[yaml_ai_context_export.txt](./yaml_ai_context_export.txt)**  
  The corresponding operational meta-prompt that produces YAML exports based on the specification.

### Prompt: Environment / Output Conventions

```
Extract and archive persistent environment-level conventions from the current context.

Include rules, constraints, or instructions that affect formatting, structure, or output behavior
across tasks, but are not part of any single task’s content.

Do not include conversational messages or task-specific requirements.

Group the extracted conventions by category where applicable, for example:
- markdown
- plantuml
- yaml
- diagrams
- general_output_rules

Preserve the original wording of each rule as closely as possible.
If a category has no applicable rules, omit it.

Output the result as structured, machine-parseable text suitable for long-term reuse.
Do not invent rules that are not explicitly present in the context.
```

Output from sample session for reference:

```yaml
markdown:
  - Do not use '---' or any horizontal rule or document delimiter.
  - Do not use numbered headings.
  - Use unnumbered Markdown headings only.
  - Avoid overly deep or fragmented heading hierarchies.
  - Do not overuse headings or create sections with only a heading and a single sentence.
  - Prefer fewer, well-populated sections with coherent paragraphs.
  - Structure Markdown for readability and manual editing.

yaml:
  - Output YAML only when YAML is requested.
  - Do not include prose, commentary, markdown, or code fences in YAML output.
  - Treat YAML output as exact and whitespace-sensitive.
  - Use consistent two-space indentation.
  - Use block style only; do not use inline or flow syntax.
  - Do not use YAML anchors, aliases, tags, or custom types.
  - Avoid implicit typing; quote scalar strings where ambiguity is possible.
  - Use literal block scalars for multi-line text.
  - Do not mix schemas in a single output.
  - Do not invent content; include only information that can be stated safely and explicitly.
  - If required information is missing or uncertain, omit the field rather than fabricating it.

general_output_rules:
  - Provide dense explanations without unnecessary repetition.
  - Avoid repeating concepts already explained elsewhere.
  - Trust the reader to read subsequent sections.
  - Separate content from control or system instructions.
  - Do not include system or developer instructions in exported artifacts.
  - Assume the user interface may not preserve formatting correctly.
  - Do not rely on the presence of a code editor or downloadable file.
  - Instruct users to copy output verbatim and avoid reformatting.
  - Treat exported output as data, not prose.
```
