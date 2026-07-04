---
name: python-imports
description: Python import style guidelines for this project. Use when writing or reviewing Python imports, adding new imports, or organizing existing import statements.
---

# Python Import Guidelines

Use absolute imports rather than relative imports, even within the same package.

Imports should be grouped in three sections separated by a blank line:

1. Standard library imports
2. Third party imports
3. Local imports

## Standard library imports

Good:

```python
import json
```

Okay when used for type annotations, or where no meaning is lost by not importing the whole module:

```python
from http import HTTPStatus
from pathlib import Path
from typing import Any, ClassVar
```

## Third party imports

Good:

```python
import requests
```

Okay when used for type annotations, or where no meaning is lost by not importing the whole module:

```python
from pydantic import BaseModel
```

## Local imports

Local imports should be grouped by module, not by function or class. This means no imports need to be updated when new module members are used. Module names should be designed so that `module.function` makes sense and is readable.

Good:

```python
from video_processing import module
```

Bad, because this code is within our control:

```python
from video_processing.module import function
```
