---
name: python-design
description: Software design principles and coding style for Python application code. Use when writing, reviewing, or refactoring Python code.
---

# Python Design Guidelines

## Design principles

Prefer, in order:

1. Pure functions: data in, data out, no side effects. (Functional core; imperative shell)
2. Dependency injection for external dependencies (databases, APIs, message queues), using `typing.Protocol` as the interface. (Dependency inversion)
3. Where side effects are unavoidable, isolate them to the edges of the system (e.g. entrypoints, request handlers). 

Optimise for simplicity and readability over cleverness or performance.

Dependencies are injected as keyword-only parameters typed to the Protocol. Concrete adapters are constructed only at the edge, never inside services.

```python
# service: business logic depends only on protocols
def process_order(
    *,
    order: Order,
    payments: protocols.PaymentGateway,
    store: protocols.OrderStore,
): ...

# edge: the entrypoint wires concrete adapters
process_order(
    order=order,
    payments=adapters.StripePayments(),
    store=adapters.PostgresOrderStore(),
)
```

Adapters satisfy Protocols structurally, not by inheritance. Do not subclass the Protocol; just match its shape.

Side-effecting service functions should delegate the computation to pure helpers (often generators), keeping the I/O loop thin.

## Typical Layering

If a codebase is complex enough to justify layering, the following is a common pattern:

* `adapters/` — domain-agnostic wrappers around external systems, plus `protocols.py` defining the ports.
* `services/` — business logic. Pure where possible; accepts injected protocols.


## Data modeling

* Domain and API data for new projects or where it is already established: `pydantic.BaseModel`, frozen via `model_config = pydantic.ConfigDict(frozen=True)`.
* Lightweight value objects: `@dataclasses.dataclass(frozen=True)`.
* Plain `dict` deliberately at external-config boundaries (e.g. builders returning payloads for external systems).
* Enums: `enum.StrEnum`. Avoid `TypedDict` and `NewType`.

## Types

Annotate all functions. Use modern syntax (`X | None`, builtin generics, `collections.abc`). Keep `mypy` passing. Use `# type: ignore` only with a justification, and only where unavoidable (e.g. third-party gaps).
