---
name: new-code-comments-and-docstrings
description: Guidelines for adding new comments or docstrings when adding or editing code in any language. Use whenever you are writing or editing code, about to write a comment or docstring, or reviewing comments in a diff.
---

These guidelines apply to all languages, including DSLs, configuration and markup formats such as YAML, and so on.

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

Prefer module, class, and function docstrings over inline comments for describing intent and architecture. Reserve inline comments for local surprises.

# Docstrings

As with comments, perefer self-documenting code. Use docstrings to summarise the purpose of a module, class, or function. Avoid explaining behaviour that is more precicely explained by the code. Avoid including superfluous information that can be inferred from the code itself.

Function docstrings should be one sentence long. A well-factored function should be explainable in a single sentence. If it requires more than that, consider breaking it into smaller functions. If, however, the function is necessarily complex, use the docstring to explain context and purpose that may not be obvious from the code itself.

The type annotations should be sufficient to describe the APIs exposed by functions.
