---
name: python-tests
description: Python test style guidelines for this project. Use when writing, reviewing, or refactoring pytest tests, test fixtures, or test doubles.
---

# Python Test Guidelines

## Structure

Tests are standalone functions at module level. Never wrap tests in classes.

Mirror the `src/` package structure under `tests/unit/` and `tests/integration/`.
Use `tests/conftest.py` for cross-cutting fixtures (settings, env patching, shared schemas).
Add local `conftest.py` files when fixtures are shared across a subdirectory but not globally.

## Naming

Test functions: `test_<action>_<scenario>` or `test_<behavior>`.
Keep names short but specific enough to identify the case in a test report.

```python
def test_transcode_with_no_audio(): ...
def test_register_duplicate_raises(): ...
def test_pixelate_scales_to_even_dimensions(): ...
```

## Comments and docstrings

Do not write comments that restate what the code does. The code is the documentation.

Use a one-line docstring only when the test name alone cannot convey
the full scenario or important context (e.g. a non-obvious business rule):

```python
def test_creating_with_operator_error():
    """Operator failures surface as TaskError, not raw exceptions."""
    ...
```

Never add section comments like `# Arrange`, `# Act`, `# Assert` —
use blank lines to separate phases visually if the test is long enough to need it.

## Fixtures over helper functions

Prefer `@pytest.fixture` for creating test data. Fixtures make dependencies
explicit in the function signature and allow pytest to manage setup/teardown.

```python
@pytest.fixture
def task_context(settings):
    return orchestration.TaskContext(
        task_id="test-task-1",
        settings=settings,
    )

def test_run_completes(task_context):
    result = service.run(task_context)
    assert result.status == "complete"
```

When the same object construction appears in 3+ tests with minor variations,
extract a fixture. If only one test needs a specific shape, inline it.

Reserve `tests/unit/fakes.py` for reusable fake implementations of Protocol interfaces.
Import as `from tests.unit import fakes` (see python-imports skill).

## Test doubles

Prefer **fakes injected via constructor** over `mocker.patch`:

```python
def test_upload(settings):
    storage = fakes.FakeCloudStorage(files={})
    service = upload.UploadService(storage=storage)
    service.upload("key", b"data")
    assert storage.files["key"] == b"data"
```

Use `mocker.patch` only for module-level functions that cannot be injected
(e.g. `get_settings`, `get_orchestration_settings`).

## Assertions

Use plain `assert` with clear expressions:

```python
assert result.status == "complete"
assert len(client.calls) == 2
```

Use `pytest.raises` with `match` for exception checks:

```python
with pytest.raises(TaskError, match="job failed"):
    service.run(context)
```

For mock call verification, use `.assert_called_once_with(...)` sparingly —
prefer inspecting the fake's recorded state over mock assertion methods.

## Parametrize

Use `@pytest.mark.parametrize` for input/output pairs where test logic is identical:

```python
@pytest.mark.parametrize("width,height,expected", [
    (1920, 1080, (1920, 1080)),
    (1921, 1081, (1922, 1082)),
])
def test_scale_to_even(width, height, expected):
    assert scale.to_even(width, height) == expected
```

Do not parametrize when different cases need different assertions or setup.

## Test length

If a test exceeds ~40 lines, consider whether it is testing multiple behaviours
and should be split. Long setup that repeats across tests is a signal to extract a fixture.
