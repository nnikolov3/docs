# Foundational Design Principles

This guide defines the non-negotiable, foundational design principles that govern all software development. Adherence to these principles is mandatory across all projects, as they are the bedrock of systems that are clear, robust, maintainable, and professional. They represent our collective commitment to quality and serve as the single source of truth that informs and unifies all language-specific coding standards. Ignoring them leads to technical debt, brittle systems, and slowed innovation.

### Core Principles

- **Simplicity is Non-Negotiable**: Strive for the most straightforward solution that fulfills all requirements. Complexity is not a measure of sophistication; it is the primary source of bugs, security vulnerabilities, and crippling maintenance overhead. A complex system is difficult to reason about, making it impossible to modify with confidence. If a design is not simple, it is wrong---re-evaluate the approach.

- **Methodical Problem Decomposition**: Rushing into a solution without a deep understanding of the problem is a primary cause of flawed software. Before writing any implementation code, you must:
  1.  **Identify and Isolate the Core Problem:** Articulate the precise problem to be solved, separating it from surrounding concerns.

  2.  **Deconstruct into Subproblems:** Break the core problem down into the smallest possible, independent, verifiable subproblems.

  3.  **Analyze Constraints and Environment:** For each subproblem, consider the limitations and features of the chosen language, framework, and environment. Identify potential edge cases, failure modes, and performance constraints up front.

  4.  **Solve Incrementally:** Address one single subproblem at a time. Follow a deliberate, robust process (such as TDD) to solve and verify each piece before integrating it into the larger solution. This methodical, step-by-step approach prevents complex, monolithic solutions and ensures each component is correct and well-understood.

- **Explicit Over Implicit**: All intentions, dependencies, and behaviors must be clear and explicit. Code should never rely on "magic" or hidden mechanisms that obscure the cause-and-effect relationship between components. Ambiguity and side effects create a system that is unpredictable and fragile. Make all assumptions visible and auditable in the code itself.

- **Self-Documenting Code and Ultimate Readability**: Code is read far more often than it is written; therefore, we optimize for the reader. The primary form of documentation is the code itself, supported by high-quality, synchronized comments.
  - **Intention-Revealing Names:** Achieve clarity through meticulously chosen, intention-revealing names for variables, functions, and classes. **Variable names must be descriptive and unambiguous; single-letter or cryptic two-letter variables are strictly forbidden.**

  - **Purpose of Comments:** Comments must explain the _why_, not the _what_. They should clarify complex logic, business rules, or the reasoning behind a specific implementation choice. If the code is so complex that it needs comments to explain _what_ it does, the code must be refactored for simplicity.

  - **Comment Quality and Hygiene:** Every source file should begin with a comment explaining its purpose and responsibility within the system. Throughout the code, comments must be clear, concise, and professional. They are a critical tool for understanding, and must be maintained with the same rigor as the code itself.

- **Single Responsibility and Low Complexity**: Every function, class, or module must have one, and only one, reason to change. It should do one thing and do it with precision and efficiency. This principle of clear responsibility separation naturally leads to code that is easier to test, reuse, and refactor. Keeping cognitive and cyclomatic complexity minimal is essential for creating components that are easy to understand and maintain.

- **Acyclic Dependencies**: The dependency graph for modules, packages, or services must be a Directed Acyclic Graph (DAG). **Circular dependencies are strictly forbidden.** They represent a critical architectural flaw that creates a "big ball of mud," making components impossible to isolate, test, or deploy independently.

- **Composition Over Inheritance**: To achieve code reuse and extend behavior, always favor composition and interfaces over implementation inheritance. Deep inheritance hierarchies lead to the "fragile base class" problem, where a change in a parent class can have unforeseen and breaking consequences for its descendants.

- **Error Handling Excellence**: Handle every error explicitly and immediately where it occurs. Never ignore or swallow exceptions. **All error messages must be explicit, provide clear context about what failed, and avoid ambiguity.** The system must fail fast and loudly, preventing it from continuing in a corrupt state.

- **Test-Driven Correctness**: Tests are a core part of the implementation itself. All components must be developed using a **test-driven development (TDD)** approach, writing a failing test _before_ the implementation code. The test suite is the ultimate proof of correctness. **Total test coverage must exceed 80% for any given project.**

- **Verifiable Truth and No Deception**: All claims about functionality must be backed by demonstrable, verifiable proof, primarily through a comprehensive suite of passing tests. Deceptive placeholders or hardcoded return values are strictly forbidden.

- **Automated Quality Enforcement**: The **extensive use of linters, formatters, and static analysis tools is mandatory and non-negotiable.** **Suppressing linter warnings with comments is strictly forbidden.** A linter warning indicates an underlying issue that must be fixed by redesigning the code, not by silencing the tool.

- **Immutability By Default**: Design components to be immutable whenever possible. This practice eliminates entire classes of bugs related to side effects and unpredictable state changes.

- **Efficient Memory Management**: Be deliberate about memory allocation and resource lifetimes. Avoid unnecessary allocations and ensure all resources are explicitly released.

- **Consistency Reduces Cognitive Load**: Follow established style guides and project conventions with rigor to create a predictable and easily understood codebase.

- **No Premature Optimization**: Write correct, clean, and simple code first. Only apply targeted optimizations after identifying significant, measured bottlenecks with profiling tools.

- **Remove What Isn't Used**: Immediately delete any dead code, unused variables, stale files, or unnecessary abstractions. **This includes stale comments; if a comment no longer accurately describes the code, it must be updated or deleted immediately.** Every line of code and every comment is a liability that must be maintained.

### Operational Mandates

#### 1\. Pre-Code Validation

- **Confirm Necessity**: Before writing any code, confirm it is absolutely necessary and does not duplicate existing functionality.

- **Justify New Code**: If new code is required, document why existing solutions are insufficient.

#### 2\. Implementation Discipline

- **Declare at the Top**: Instantiate all variables at the top of their respective scope.

- **No Magic Numbers**: Use named constants instead of hardcoded values.

- **Parameterize Everything**: Strictly decouple application logic from configuration.

#### 3\. Verification Loop

- The development cycle is a tight, continuous loop: **Test, Implement, Lint, Format, Refactor, Repeat.**

- All code must pass all automated quality checks on every change.

#### 4\. Security as a Default

- **Least Privilege**: Components should only have the permissions they absolutely need.

- **Validate at Boundaries**: Rigorously validate and sanitize all external data.

- # **Never Log Secrets**: Secrets must never be written to logs or stored in plaintext.Strict Go Coding Standard

This document establishes the non-negotiable Go coding standards for all projects. It is aligned with modern Go (1.23+) and is a direct implementation of the core tenets found in the `DESIGN_PRINCIPLES_GUIDE.md`. All code must strictly adhere to these rules to ensure it is simple, robust, verifiable, and maintainable.

### 1\. Core Principles: The Go Implementation

All code is a direct reflection of the **`DESIGN_PRINCIPLES_GUIDE.md`**. This includes:

- **Test-Driven Correctness (TDD)**: Mandatory workflow. **Test coverage must exceed 80%.**

- **Automated Quality Enforcement**: Must pass `golangci-lint` with zero errors. **Suppressing linter warnings with comments (e.g., `//nolint`) is strictly forbidden.** A linter warning signals a design flaw that must be fixed, not silenced.

- **Self-Documenting Code**: Use intention-revealing names. **Single-letter or cryptic variables are forbidden.**

- **Simplicity and Clarity**: Write simple, readable code.

- **Acyclic Dependencies**: No circular dependencies.

- **Single Responsibility**: Functions and interfaces must be small and focused.

- **Composition Over Inheritance**: Use struct embedding and interfaces.

- **Error Handling Excellence**: Handle every `error` explicitly and immediately.

- **Immutability By Default**: Avoid mutating state where possible.

- **Efficient Memory Management**: Be deliberate about allocations.

### 2\. Automated Quality Enforcement: The Toolchain

We do not manually enforce style or catch common errors. We automate it with a comprehensive toolchain that provides a non-negotiable quality gate.

- **Formatter (`gofmt`, `gofumpt`)**: Standard Go formatting is not optional.

- **Comprehensive Linter (`golangci-lint`)**: This tool is mandatory and configured to enforce all standards in this document.

Code that does not pass `gofmt` and `golangci-lint` is, by definition, incomplete and will be rejected.

### 3\. Self-Documenting Code: Naming and Documentation

Names must be explicit and intention-revealing. Use `camelCase` for unexported and `PascalCase` for exported identifiers. Every exported identifier must have a `godoc` comment.

### 4\. Complexity and Maintainability Metrics

To enforce **Simplicity**, all code is measured against the following metrics enforced by `golangci-lint`.

- **Function Length**: Max **60 lines** or **40 statements**.

- **Cyclomatic Complexity**: Max **10** per function.

- **Cognitive Complexity**: Max **5** per function.

Code exceeding these thresholds must be refactored.

### 5\. Package and Module Design

- **Package Naming**: Use lowercase, descriptive names. Avoid generic names like `util` or `common`.

- **Interfaces**: Define small, focused interfaces in the package that _consumes_ the functionality ("accept interfaces, return structs").

### 6\. Function and Method Design

- **Single Responsibility**: A function should do one thing. If its description requires the word "and," it must be refactored.

- **Return Pointers for Large Structs**: To adhere to **Efficient Memory Management**, functions returning structs larger than a small, fixed size should return a pointer (`*MyStruct`) instead of the struct by value (`MyStruct`) to avoid unnecessary copying on the stack.

### 7\. Error Handling Patterns

- **No Error Variable Reassignment**: To prevent subtle bugs from shadowed variables, **every call that returns an error must use a new, uniquely named error variable.** Reassigning a general `err` variable is strictly forbidden.

  ```
  // ❌ BAD: 'err' is reassigned, which can hide the first error.
  file, err := os.Open("config.yaml")
  if err != nil {
      // ...
  }
  data, err := io.ReadAll(file) // This re-declaration is a common bug.
  if err != nil {
      // ...
  }

  // ✅ GOOD: Each error has a unique variable, preventing bugs.
  file, openErr := os.Open("config.yaml")
  if openErr != nil {
      return fmt.Errorf("failed to open config file: %w", openErr)
  }
  defer file.Close()

  data, readErr := io.ReadAll(file)
  if readErr != nil {
      return fmt.Errorf("failed to read config file: %w", readErr)
  }

  ```

- **Error Wrapping**: Always wrap errors with `fmt.Errorf("...: %w", err)` to add context.

- **Immediate Handling**: Check for errors immediately after a function call.

### 8\. Concurrency

- **Context is Mandatory**: Pass a `context.Context` as the first argument to any function that performs I/O, makes a network call, blocks, or could be long-running.

- **Channels for Communication**: Use channels to communicate between goroutines. Do not communicate by sharing memory.

- **Graceful Shutdown**: Ensure all long-running goroutines can be shut down gracefully.

### 9\. Testing Standards

- **Framework**: Use the standard `testing` package with `testify/assert` or `testify/require`.

- **TDD is Mandatory**: Follow a test-driven workflow.

- **Table-Driven Tests**: Use table-driven tests for testing multiple scenarios concisely.
