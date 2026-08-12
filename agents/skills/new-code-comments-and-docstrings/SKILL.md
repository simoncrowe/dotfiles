---
name: new-code-comments-and-docstrings
description: Guidelines for adding new comments or docstrings when adding or editing code in any language. ALWAYS use when you are writing or editing code, about to write a comment or docstring, or reviewing comments in a diff.
---

These guidelines apply to all languages, including DSLs, configuration and markup formats such as YAML, and so on.

Generally keep comments and docstrings to a minimum and as short as possible. Each word should earn its place as useful to a future human reader. DO NOT restate code in prose. DO NOT mention ticket numbers or requirements (e.g. CORE-1234, R1, R2). DO NOT mention spec-driven "plan" requirements (e.g. REQ-017, REQ-024, TASK-002). Don't mention how a low-level component is _currently_ used by a high-level component, as this could change. Don't explain why something is implemented in a particular way; keep that context to yourself.

# Code Comments

Keep comments to a minimum. Aim for self-documenting code: clear names and structure remove the need for most comments.

Comment only when the code does something not obvious from its context, and then explain *why*, not *what*.

Good — explains a non-obvious decision:

```python
# Do not re-raise the exception after writing a 'failed' status.
# This status is final and kubernetes-level retries are reserved for
# unhandled exceptions caused by transient issues like network errors.
```

Good — points to the reason behind a workaround:

```python
# Temporary fix for CORE-3900 until the root cause is identified
```

Bad — restates the code:

```python
# increment the counter
counter += 1
```

Bad — references requirements from a plan document (or external ticket).

```python
:# frees only a lock this run holds (REQ-024).
```

Prefer module, class, and function docstrings over inline comments for describing intent and architecture. Even when using docstrings, keep the content terse and relevant to a future reader who is in a hurry. Reserve inline comments for local surprises.

# Docstrings

As with comments, prefer self-documenting code. Use docstrings to summarise the purpose of a module, class, or function. Keep these summaries short and to the point.

Avoid explaining behaviour that is more precisely explained by the code. Avoid including superfluous information that can be inferred from the code itself. Do not reference how code is used in a higher-level module; This usage could change.

Aim for a single paragraph of prose, at most, for a module docstring. The goal is to introduce the domain of the module, NOT explain everything it does and NOT why it’s implemented as it is.

Function docstrings should be one sentence long if included at all. A well-factored function should be explainable in a single sentence. If it requires more than that, consider breaking it into smaller functions. If, however, the function is necessarily complex, use the docstring to explain context and purpose that may not be obvious from the code itself.

Rely only on type annotations to describe the shape of APIs exposed by functions.
