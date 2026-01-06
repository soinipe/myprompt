# Machine-Parseable Export of AI Conversation Context

## Summary and purpose

A machine-parseable export of AI conversation context is a **structured, deterministic interface** between AI systems. By clearly separating conversational logs from derived task state, enforcing strict formatting rules, and using explicit target selection, such exports enable safe reuse, automation, and long-term maintainability across heterogeneous AI environments.

The **design principle** is conservative context propagation, defined by the question:  *“What can be safely, explicitly, and non-hallucinatorily carried forward?”*

Information whose presence, correctness, or scope is uncertain is intentionally omitted in order to prevent false certainty, silent data corruption, and the propagation of incorrect assumptions into downstream systems.

## How to use this specification

This specification defines a structured way to export AI conversation context for reuse by other AI systems with different architectures.

To use it, select exactly one target consumer in the `TARGET_CONSUMER` block of the meta-prompt [yaml_ai_context_export.txt](./yaml_ai_context_export.txt). The selected target specifies the intended AI system and determines which export schema is produced.

Apply the meta-prompt to the conversation to be exported. The result is a YAML-only output that conforms to the schema associated with the selected target and to the formatting constraints defined later in this document.

The exported YAML is intended to be consumed programmatically or stored as structured data. Its meaning, structure, and constraints are fully defined in the sections that follow.

### Handling YAML output safely in different AI interfaces

The meta-prompt is not designed to provide a means to download the YAML output, because interface capabilities vary widely across AI systems and cannot be controlled by the prompt itself. In particular, the prompt does not assume that the AI system provides a code editor, file view, or file download mechanism. Users are therefore expected to take deliberate steps to obtain the output safely, depending on the capabilities of the interface in use.

The following example prompt patterns may help, depending on what the AI system supports:

- *Produce the YAML output as a downloadable file without modifying its content.*
- *Open the YAML output in a file or code editor view so it can be copied without reformatting.*
- *Output the YAML as plain text exactly as generated, without markdown formatting, line wrapping, or additional explanation. Preserve all spaces and line breaks.*

Users should assume that manual verification may still be necessary, as transport interfaces can silently corrupt whitespace, and any change to indentation or line breaks may render the YAML invalid.

## Overview and background

This document explains the concept of a **machine-parseable export of AI conversation context**, why it is useful, and how a target-aware meta-prompt is structured to produce such exports reliably. It also defines all relevant terminology used in the specification.

The focus is on **clarity, precision, and interoperability** across different AI systems.

## What “machine-parseable export” means in AI systems

A **machine-parseable export** is a representation of information that:

- Follows a strict, well-defined structure
- Can be read and interpreted programmatically without guesswork
- Avoids ambiguity, free-form prose, or implicit assumptions

In the context of AI systems, a machine-parseable export of conversation context is **not meant for humans first**. Instead, it is designed so that:

- Other software systems can consume it
- Automation pipelines can validate it
- Other AI models can reuse it safely
- Humans can still inspect it if needed

In practice, this usually means using a structured data format such as **YAML or JSON** with clear rules and schemas.

### Why machine-parseable context exports are useful

Machine-parseable context exports are commonly used when:

- Context must be transferred between different AI systems
- Conversations need to be archived or audited
- AI agents need to resume work later
- Context must be transformed into task-specific state
- Toolchains need deterministic, repeatable inputs
- The system requiring the session to be terminated and continued in a new one, even though the task itself is not complete

Typical usage scenarios include:

- Handing off context from a chat assistant to an IDE assistant
- Persisting AI state between sessions
- Feeding structured context into automation or agent frameworks
- Debugging or auditing AI behavior
- Supporting compliance and traceability requirements

Long-running AI sessions often degrade over time. As conversations grow, context windows become saturated, earlier information may be truncated, and response latency can increase. In task-embedded environments, this frequently forces users to terminate a session and continue work in a new one, even though the task itself is not complete.

The key idea is that **conversation history is data**, and data should be exported in a form that machines can reliably process.

## Separating conversation content from control instructions

### Why system and developer instructions are excluded

System and developer instructions are not part of the conversation in the usual sense. They exist to control how the assistant behaves rather than to express what the user and assistant are discussing. These instructions define things such as the assistant’s role, safety constraints, formatting rules, and other behavioral requirements imposed by the platform or application. The actual conversation happens *within* these constraints, but the constraints themselves are not the subject of the conversation and should not be treated as transferable context.

### Control instructions versus conversation content

When exporting context, it is essential to separate **content** from **control logic**. Conversation content consists of what the user asked and what the assistant responded, or — in the case of task-oriented exports — the task state that can be derived from that exchange. System and developer instructions belong to a different layer: they describe *how* the assistant should operate, not *what* is being discussed. Mixing these two layers would blur responsibilities and make the exported context harder to interpret correctly.

### Risks of exporting system and developer instructions

Including system or developer instructions in a context export can lead to problems when the export is reused. A downstream system might unintentionally reapply those instructions, apply conflicting rules multiple times, or import constraints that are incompatible with its own environment. This can result in unpredictable behavior and subtle failures. In addition, such instructions often contain platform-specific or internal details that are not meant to be shared or transferred.

### Why exclusion is required

For a machine-parseable context export to be portable, safe, and reusable, it must contain only the meaningful outcomes of the interaction: the dialogue itself or the structured task state derived from it. Excluding system and developer instructions ensures that the export remains focused on intent and content, avoids leaking internal scaffolding, and prevents accidental prompt injection when the export is consumed by another system.

## Export data format

### Why YAML was selected as the export format

The choice of YAML as the export format is intentional and driven by the specific requirements of machine-parseable AI context exports. While formats such as Markdown (MD) and JSON are also widely used, YAML provides the best balance between **machine reliability** and **human usability** for this use case.

Below is a structured explanation of why YAML was selected and how it compares to alternatives.

### Core requirements for a context export format

A machine-parseable context export must satisfy all of the following:

- Be reliably parseable by software
- Preserve structured data without ambiguity
- Support strict schemas and validation
- Allow safe human inspection and editing
- Be resilient to partial deletion or pruning
- Avoid excessive verbosity or syntactic noise

YAML meets these requirements better than the alternatives in this specific domain. YAML was chosen because it balances **machine structure** with **human affordances**. Advantages of YAML:
- Fully machine-parseable with mature tooling
- Naturally represents mappings, sequences, and text
- Supports multi-line content cleanly
- More readable than JSON
- Easier and safer for humans to edit
- Lower syntactic noise
- Better diff-friendliness

When constrained to a restricted subset (as in this specification), YAML becomes both safe and predictable.

YAML has certain restrictions: an important distinction

Unrestricted YAML can be ambiguous or error-prone.  This specification intentionally limits YAML features to ensure reliability.

Key restrictions include:
- Two-space indentation
- Block style only (no inline flow syntax)
- No anchors, aliases, or tags
- No implicit typing
- Literal block scalars for multi-line text

With these constraints, YAML behaves much like a **human-friendly structured data format**, without sacrificing determinism.

### Alternative: Markdown

Markdown is designed for **human-readable documents**, not structured data exchange. Limitations of Markdown for context exports:
- No formal schema or type system
- Ambiguous structure (headings and lists are semantic, not structural)
- Requires conventions to parse reliably
- Difficult to validate automatically
- Easy for humans to accidentally break structure
- Poor round-trip safety (edit → parse → re-export)

Markdown is excellent for documentation and review, but it is **not a data format**.

Markdown is well suited for:
- Human review of exported context
- Documentation and specifications
- Summaries and explanations

It is **not suitable** as the canonical export format for automated systems.

### Alternative: JSON

JSON is a strong machine-oriented format, but it has drawbacks in this use case.

Limitations of JSON for AI context exports:
- No native comment support
- Very strict syntax (small errors break parsing)
- Poor human editability for large documents
- High visual noise (braces, commas, quotes)
- Difficult to safely remove or comment out blocks
- Less readable for multi-line text

JSON optimizes for machines at the expense of humans.

JSON is ideal when:
- The export is fully machine-generated and machine-consumed
- No human editing is expected
- Strict API integration is required

For AI context exports that may be inspected, pruned, or adapted by humans, JSON is less practical.

## Overview of the meta-prompt

The meta-prompt defines **how an AI should export conversation context**, depending on the intended downstream consumer.

It is *target-aware*, meaning the same conversation can be exported differently depending on how it will be used.

The meta-prompt achieves this by:

- Explicitly selecting a target consumer
- Choosing one of multiple export schemas
- Enforcing strict output and formatting rules
- Preventing hallucinated or invented content

## The purpose of the meta-prompt

The meta-prompt exists to:

- Standardize how AI conversation context is exported
- Prevent ambiguity and ad-hoc formatting
- Ensure exports are safe for automation
- Separate conversational history from derived task state
- Make the output self-describing through metadata

It treats context export as an **interface contract**, not a casual summary.

## Why ChatGPT-like and Copilot-like systems require different context exports

At first glance, ChatGPT and Copilot may appear similar: both are AI systems powered by large language models, both respond to natural language, and both assist users with problem-solving. However, **they operate on fundamentally different context models**, which makes a single “one-size-fits-all” context export impractical.

Understanding this difference explains why separate targets TARGET_CONSUMER = (`chatgpt_like`, `copilot_like`) are required.

## ChatGPT-like systems: conversation-centric context

### How ChatGPT-like systems work

ChatGPT-like systems are built around a **conversational interaction model**. Their primary input is a **sequence of messages**, each associated with a role (user or assistant), ordered over time.

Key characteristics:

- Context is **linear and chronological**
- Meaning is carried by **dialogue flow**
- Previous turns influence interpretation of later turns
- The system is optimized to *continue a conversation*

In these systems, context is best represented as a **conversation log**.

### What matters to ChatGPT-like systems

ChatGPT-like systems rely on:

- The exact wording of prior messages
- The order in which things were said
- Role distinctions (who said what)
- Implicit conversational cues

Because of this, **lossless replayability** is important. If you want to resume or continue a conversation, you must preserve the dialogue structure.

### Implication for context export

For ChatGPT-like systems:

- Exporting the **conversation itself** makes sense
- Summarization or abstraction would destroy conversational meaning
- The export must preserve:
  - Order
  - Roles
  - Full message content

This leads directly to the `chatgpt_like` export schema.

## Copilot-like systems: task-embedded context

### How Copilot-like systems work

Copilot-like systems are **embedded assistants**, not free-form chat systems. They operate *inside* another application or workflow, such as:

- An IDE
- A document editor
- A project management tool
- An operating system shell

Their primary purpose is **task continuation**, not conversation continuation.

### Context is not a conversation

In Copilot-like systems:

- Context comes from the **environment**, not from chat history
- Inputs may include:
  - Files or code snippets
  - Cursor position
  - Selected text
  - Project structure
  - Application state

Conversation, if present at all, is secondary and often ephemeral.

What matters is **what needs to be done**, not how the user and assistant talked about it.

## Task state vs conversation history

Because Copilot-like systems are task-oriented, they care about:

- The user’s goal
- Constraints and requirements
- Decisions already made
- Open questions
- Referenced artifacts

They do **not** benefit from replaying conversational turns such as:

> “Yes, that sounds good.”
> “Let’s do it this way.”

Those statements only matter insofar as they imply decisions or intent.

### Derived task state

To support Copilot-like systems, conversation must be **interpreted and distilled** into a structured form called **derived task state**.

Derived task state:

- Is extracted from conversation
- Removes dialogue and roles
- Preserves meaning relevant to the task
- Is not replayable as a chat

This is why the `copilot_like` export schema explicitly forbids conversational formatting.

## Why a single export format does not work

Attempting to use one format for both systems leads to problems.

### Conversation logs are poor input for Copilot-like systems

- Too verbose
- Too noisy
- Lack explicit task structure
- Waste context window on irrelevant dialogue

### Task state is insufficient for ChatGPT-like systems

- Loses conversational nuance
- Cannot be replayed
- Breaks continuity
- Prevents natural follow-up interaction

Each system optimizes for a **different kind of continuity**:

- ChatGPT-like systems preserve *conversational continuity*
- Copilot-like systems preserve *task continuity*

## Target mechanism

The `TARGET_CONSUMER` mechanism exists to:

- Make the intended consumer explicit
- Prevent the AI from guessing how to export context
- Enforce the correct mental model
- Avoid silent data loss or misinterpretation

By selecting a target explicitly, the meta-prompt ensures that:

- Conversation logs remain conversation logs
- Task state remains task state
- Downstream systems receive context in the form they are designed to consume

## Summary about different LLM systems

ChatGPT-like and Copilot-like systems differ not in *capability*, but in **how they consume context**:

- ChatGPT-like systems think in **dialogue**
- Copilot-like systems think in **tasks**

A machine-parseable context export must respect this difference.
The use of separate targets is therefore not a convenience, but a **necessary architectural distinction**.

## Meta-Prompt Technical Specification

This section introduces the structural control block that governs how the meta-prompt selects and produces the correct export schema.

This section explains:

- The purpose of the selector
- How the checkbox mechanism works
- How the AI interprets the selection
- Why strict determinism is required

## The TARGET_CONSUMER selector

The `TARGET_CONSUMER` determines **who or what will consume the exported context**.

Two consumers are defined:

- `chatgpt_like`
- `copilot_like`

Each consumer expects context in a fundamentally different form.

The selector ensures the AI does not guess how to format the output.

## The checkbox-based selection mechanism

The meta-prompt uses a checkbox pattern:

```
- chatgpt_like  [  ]
- copilot_like  [ X ]
```

This mechanism is:

- Human-friendly
- Explicit
- Deterministic

Only one checkbox is marked with an `X`, making the selection unambiguous.

## The interpretation rule

The interpretation rule defines how the AI must read the checkboxes:

- If `chatgpt_like` has `[ X ]`, then the target is `chatgpt_like`
- If `copilot_like` has `[ X ]`, then the target is `copilot_like`

This prevents the AI from inferring intent based on surrounding text or prior context.

## The ChatGPT-like export schema

### Purpose

The ChatGPT-like schema is designed for **conversation replay** and **dialogue continuation**.

It exports a **conversation log**.

### Characteristics

- Chronological order
- Role-based messages (`user`, `assistant`)
- No summarization or abstraction
- Suitable for re-ingestion by conversational models

### Structure example

```
metadata:
  target_consumer: chatgpt_like
  context_type: conversation_log
  export_scope: visible_context_only
  conversation_replayable: true
  truncation_possible: true

conversation:
  - role: user
    content: |
      How does context export work?
  - role: assistant
    content: |
      Context export works by…
```

## The Copilot-like export schema

### Purpose

The Copilot-like schema is designed for **task continuation**, not conversation replay.

It exports **derived task state**.

### Characteristics

- No dialogue or speaker roles
- No chronological turns
- Structured, task-oriented information
- Optimized for embedded or IDE-style assistants

### Structure example

```
metadata:
  target_consumer: copilot_like
  export_scope: derived_task_state
  context_type: task_state
  conversation_replayable: false
  truncation_possible: true

task_state:
  goal: |
    Design a target-aware context export specification.
  functional_requirements:
    - |
      Output must be YAML-only and machine-parseable.
  design_decisions:
    - |
      Use a checkbox-based target selector.
  unresolved_questions:
    - |
      Should additional target consumers be supported?
  referenced_artifacts:
    - |
      Context export meta-prompt specification.
```

## The YAML formatting constraints

The meta-prompt enforces a restricted YAML subset to ensure consistency and safety.

Key constraints include:

- Two-space indentation only
- Block style only (no inline `{}` or `[]`)
- No anchors, aliases, tags, or custom types
- No implicit typing (strings must be quoted)
- Block scalars (`|`) for multi-line text

These rules reduce parser incompatibilities and prevent subtle data corruption.

## Rules about not inventing content

The AI is explicitly forbidden from:

- Guessing missing information
- Filling in plausible-sounding details
- Adding interpretations not present in the conversation

All exported content must be **derived directly from the provided context**.

This is critical for trust, auditing, and automation.

## Rules about omitting missing fields

If a field cannot be populated from the conversation:

- The field must be omitted
- Placeholder values must not be used

This avoids false completeness and prevents downstream systems from relying on fabricated data.

## The requirement for pure YAML output

The final output must be:

- YAML only
- No prose
- No explanations
- No code fences
- No surrounding text

This ensures the export can be:

- Parsed without preprocessing
- Validated automatically
- Stored or transmitted as structured data

## Terminology definitions

### Context

The information provided to an AI model at inference time, including conversation history, instructions, and relevant state.

### Conversation log

A chronological record of messages exchanged between a user and an assistant, including roles and message content.

### Task state

A structured representation of what needs to be done, including goals, requirements, decisions, and open questions.

### Derived task state

Task state that has been **extracted and structured** from a conversation rather than copied verbatim.

### Export scope

A descriptor indicating what portion or interpretation of context the export represents.

### Conversation-replayable

A boolean indicating whether the export can be reused as input to replay or continue a conversation.

### Truncation

The loss of earlier conversation content due to context window limits or system constraints.

### Metadata

Structured descriptive information about the export itself, used to guide downstream interpretation and handling.

