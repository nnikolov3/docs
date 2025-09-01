# Python Coding Standard

This document establishes the strict Python coding standards for all projects, aligned with modern Python (3.11+) and the core tenets of the `DESIGN_PRINCIPLES_GUIDE.md`.

### Core Principles

All code must strictly adhere to the foundational principles outlined in the **`DESIGN_PRINCIPLES_GUIDE.md`**. This includes, but is not limited to:

- **Simplicity and Clarity**: Write simple, readable code.

- **Acyclic Dependencies**: Ensure a Directed Acyclic Graph (DAG) for all imports.

- **Single Responsibility**: Classes and functions must do one thing well.

- **Composition Over Inheritance**: Use composition for code reuse.

- **Error Handling Excellence**: Handle all exceptions explicitly.

### Module and Package Organization

- **Package Naming**: Use lowercase, short, and descriptive package names. **Forbidden Names**: Avoid generic names like `utils`, `common`, or `helpers`. Names must describe the domain functionality (e.g., `audio_playback`, `text_processing`).

- **Dependency Management**: Module and package dependencies must form a Directed Acyclic Graph (DAG). **Circular imports are strictly forbidden** and must be resolved by refactoring the code structure. They are a sign of a critical design flaw.

- **Import Organization**: Group imports in the following order, separated by blank lines: 1. Standard library, 2. Third-party libraries, 3. Local application/library-specific imports. Use absolute imports.

### Class and Interface Design

- **Class Design**: Keep classes small and focused, adhering to the Single Responsibility Principle. A class should have only one reason to change.

- **Interfaces**: Use Abstract Base Classes (`abc`) or Protocols (`typing.Protocol`) to define clear contracts for behavior.

- **Memory Efficiency**: Use `__slots__` for classes that will be instantiated many times to reduce memory footprint.

### Error Handling Patterns

- **Custom Exceptions**: Define a custom exception hierarchy for your application's domain to provide specific and meaningful error information.

- **Exception Chaining**: Use exception chaining (`raise NewException from original_exception`) to preserve the root cause of an error, which is essential for debugging.

- **Boundary Validation**: Validate all inputs at function and method boundaries. Raise `ValueError` or a custom exception for invalid data.
