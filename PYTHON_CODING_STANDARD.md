# Strict Python Coding Standard

This document establishes the non-negotiable Python coding standards for all projects. It is aligned with modern Python (3.11+) and is a direct implementation of the core tenets found in the `DESIGN_PRINCIPLES_GUIDE.md`. All code must strictly adhere to these rules to ensure it is simple, robust, verifiable, and maintainable.

### 1\. Core Principles: The Pythonic Implementation

All code is a direct reflection of the **`DESIGN_PRINCIPLES_GUIDE.md`**. This includes:

- **Test-Driven Correctness**: All logic must be developed using a test-driven development (TDD) workflow. A failing test must be written before the implementation. The test suite is the ultimate proof of correctness.

- **Automated Quality Enforcement**: Code must be formatted with `black`, and it must pass all `ruff` and `mypy` checks without any suppression flags before it can be considered complete.

- **Self-Documenting Code**: Prioritize clarity through intention-revealing names and mandatory type hints. Code must explain itself.

- **Simplicity and Clarity**: Write simple, readable code. Avoid complex one-liners or "clever" constructs.

- **Acyclic Dependencies**: Ensure a Directed Acyclic Graph (DAG) for all imports. Circular imports are a critical design flaw and are forbidden.

- **Single Responsibility**: Classes and functions must do one thing well.

- **Composition Over Inheritance**: Use composition and interfaces (`typing.Protocol`) for code reuse and flexibility.

- **Error Handling Excellence**: Handle all exceptions explicitly. Never use a bare `except:`.

### 2\. Automated Quality Enforcement: The Toolchain

We do not manually enforce style. We automate it. The following toolchain is mandatory:

- **Formatter (`black`)**: Ensures uniform code style.

- **Linter (`ruff`)**: Catches a wide range of errors, style issues, and bad practices.

- **Type Checker (`mypy`)**: Statically analyzes type hints to prevent common bugs.

Code that does not pass these automated checks is, by definition, incomplete.

### 3\. Self-Documenting Code: Naming, Typing, and Docstrings

Code is read more than it is written. Optimize for the reader.

#### Naming Conventions

Names must be explicit and intention-revealing.

```
# ❌ BAD: Ambiguous, single-letter, or abbreviated names
def proc_data(d: list) -> list:
    # ... what is 'd'? What does 'proc' mean?
    pass

# ✅ GOOD: Clear, descriptive, and explicit names
def parse_user_profiles_from_json(raw_json_data: list[dict]) -> list[UserProfile]:
    # The name clearly states its purpose, input, and output.
    pass

```

#### Type Hints are Mandatory

Every function and method signature **must** include type hints for all arguments and the return value. This is a requirement for **Self-Documenting Code** and enables static analysis.

```
# ❌ BAD: No type hints, the reader must guess the data types.
def combine_records(users, permissions):
    # What are users? A list of strings? A dict?
    return {**users, **permissions}

# ✅ GOOD: Types are explicit, enabling clarity and static analysis.
from typing import Dict

def combine_user_permissions(
    users: Dict[str, User],
    permissions: Dict[str, PermissionLevel]
) -> Dict[str, CombinedProfile]:
    # ... implementation ...

```

### 4\. Module and Package Organization

Structure code to be logical and maintainable.

- **Package Naming**: Use lowercase, short, and descriptive package names.
  - **Forbidden Names**: Avoid generic names like `utils`, `common`, or `helpers`. These names become a dumping ground for unrelated code and violate the Single Responsibility Principle. Names must describe the domain functionality (e.g., `audio_playback`, `text_processing`).

- **Dependency Management**: Module and package dependencies must form a Directed Acyclic Graph (DAG). **Circular imports are strictly forbidden** and must be resolved by refactoring. `ruff` will help detect these.

- **Import Organization**: `ruff --fix` (with `isort` rules enabled) will handle this automatically. The standard order is:
  1.  Standard library (`import os`)

  2.  Third-party libraries (`import requests`)

  3.  Local application/library-specific imports (`from . import models`)

### 5\. Function and Method Design

Functions are the primary unit of logic and must be kept small and focused.

- **Single Responsibility**: A function should do one thing and do it well. If a function description requires the word "and," it is likely doing too much.

```
# ❌ BAD: This function has multiple responsibilities.
def process_and_upload_report(data: list[dict], user_id: str):
    # 1. It generates a report.
    report_path = f"/tmp/report_{user_id}.csv"
    with open(report_path, "w") as f:
        # ... write csv data ...

    # 2. It uploads the report.
    s3_client.upload_file(report_path, "reports-bucket", f"{user_id}/report.csv")
    return True

# ✅ GOOD: Responsibilities are separated into focused functions.
def generate_user_report(data: list[dict], output_path: Path) -> None:
    """Generates a CSV report from data and saves it to a file."""
    # ... implementation ...

def upload_file_to_s3(local_path: Path, bucket: str, remote_key: str) -> None:
    """Uploads a single file to an S3 bucket."""
    # ... implementation ...

```

### 6\. Class and Interface Design

Use classes to encapsulate state and behavior, and interfaces to define contracts.

- **Class Design**: Keep classes small and focused. A class should have only one reason to change. Favor composition over deep inheritance hierarchies.

- **Interfaces (`typing.Protocol`)**: Use protocols to define clear contracts for behavior, enabling dependency inversion and making code easier to test.

```
# ✅ GOOD: Using a Protocol to define a dependency contract.
from typing import Protocol

class Notifier(Protocol):
    """A protocol for any object that can send a notification."""
    def send(self, message: str) -> None:
        ...

# The function depends on the contract, not a concrete implementation.
def process_critical_event(event: Event, notifier: Notifier) -> None:
    """Processes an event and sends a notification."""
    # ... logic ...
    notifier.send(f"Event {event.id} processed successfully.")

# Concrete implementations can be easily swapped (e.g., for testing).
class EmailNotifier:
    def send(self, message: str) -> None:
        # ... send email ...

class SlackNotifier:
    def send(self, message: str) -> None:
        # ... send slack message ...

```

- **Memory Efficiency (`__slots__`)**: For classes that will be instantiated many times and have a fixed set of attributes, use `__slots__` to reduce memory footprint by preventing the creation of an instance `__dict__`.

```
# ✅ GOOD: For data-heavy applications.
class DataPoint:
    __slots__ = ('timestamp', 'value', 'source')

    def __init__(self, timestamp: float, value: float, source: str):
        self.timestamp = timestamp
        self.value = value
        self.source = source

```

### 7\. Error Handling Patterns

Robust error handling is non-negotiable.

- **Custom Exceptions**: Define a custom exception hierarchy for your application's domain. This allows calling code to handle specific error conditions gracefully.

```
# ✅ GOOD: A clear exception hierarchy.
class ReportError(Exception):
    """Base exception for reporting errors."""
    pass

class ReportGenerationError(ReportError):
    """Raised when the report content cannot be generated."""
    pass

class ReportUploadError(ReportError):
    """Raised when uploading the report fails."""
    pass

```

- **Exception Chaining**: Always use exception chaining (`raise NewException from original_exception`) to preserve the root cause of an error. This is essential for effective debugging.

```
def upload_report(report: Report) -> None:
    try:
        s3_client.upload(...)
    except S3ClientError as e:
        # Preserve the original S3 error for debugging.
        raise ReportUploadError("Failed to upload report to S3.") from e

```

- **Boundary Validation**: Validate all inputs at function and method boundaries. Fail fast with a clear error message if the inputs are invalid.

```
def create_user(username: str) -> None:
    if not username or not username.isalnum():
        raise ValueError("Username must be non-empty and alphanumeric.")
    # ... proceed with user creation ...

```

### 8\. Testing Standards

Tests are the ultimate proof of correctness.

- **Framework**: Use `pytest` for all testing.

- **TDD is Mandatory**: Follow a test-driven workflow. Write a failing test that defines the desired behavior before writing the implementation.

- **Test Isolation**: Tests must be independent and runnable in any order. Use `pytest` fixtures for setup and teardown to ensure a clean state for each test.

- **Assert Exceptions**: When testing for expected errors, use `pytest.raises`.

```
# ✅ GOOD: A clear, focused test using pytest.
import pytest

def test_create_user_with_invalid_username_raises_value_error():
    """Verify that an invalid username raises a ValueError."""
    with pytest.raises(ValueError, match="Username must be non-empty"):
        create_user("")

    with pytest.raises(ValueError, match="alphanumeric"):
        create_user("invalid-user!")

```
