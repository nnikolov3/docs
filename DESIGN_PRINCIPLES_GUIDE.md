# Foundational Design Principles

This guide defines the non-negotiable, foundational design principles that govern all software development. Adherence to these principles is mandatory across all projects, as they are the bedrock of systems that are clear, robust, maintainable, and professional. They represent our collective commitment to quality and serve as the single source of truth that informs and unifies all language-specific coding standards. Ignoring them leads to technical debt, brittle systems, and slowed innovation.

### Core Principles

- **Simplicity is Non-Negotiable**: Strive for the most straightforward solution that fulfills all requirements. Complexity is not a measure of sophistication; it is the primary source of bugs, security vulnerabilities, and crippling maintenance overhead. A complex system is difficult to reason about, making it impossible to modify with confidence. If a design is not simple, it is wrong---re-evaluate the approach.

- **Explicit Over Implicit**: All intentions, dependencies, and behaviors must be clear and explicit. Code should never rely on "magic" or hidden mechanisms that obscure the cause-and-effect relationship between components. Ambiguity and side effects create a system that is unpredictable and fragile. Make all assumptions visible and auditable in the code itself.

- **Self-Documenting Code and Ultimate Readability**: Code is read far more often than it is written; therefore, we optimize for the reader. The primary form of documentation is the code itself. Achieve this through meticulously chosen, intention-revealing names for variables, functions, and classes. The code's structure and flow should narrate its purpose. Comments should only explain _why_ a particular approach was taken, never _what_ the code is doing---if the "what" is not obvious, the code is not clear enough and must be refactored.

- **Single Responsibility and Low Complexity**: Every function, class, or module must have one, and only one, reason to change. It should do one thing and do it with precision and efficiency. This principle naturally leads to code that is easier to test, reuse, and refactor. Keeping cognitive and cyclomatic complexity minimal is essential for creating components that are easy to understand and maintain.

- **Acyclic Dependencies**: The dependency graph for modules, packages, or services must be a Directed Acyclic Graph (DAG). **Circular dependencies are strictly forbidden.** They represent a critical architectural flaw that creates a "big ball of mud," making components impossible to isolate, test, or deploy independently. A circular dependency is a symptom of poor boundaries and must be refactored immediately by inverting the dependency or extracting a new, shared component.

- **Composition Over Inheritance**: To achieve code reuse and extend behavior, always favor composition and interfaces over implementation inheritance. Deep inheritance hierarchies lead to the "fragile base class" problem, where a change in a parent class can have unforeseen and breaking consequences for its descendants. Composition results in more flexible, modular, and understandable designs.

- **Error Handling Excellence**: Handle every error explicitly and immediately where it occurs. Never ignore or swallow exceptions, as this leaves the system in an unknown state. Provide rich, contextual error information and use wrapping/chaining to preserve the original cause, which is invaluable for debugging. The system must fail fast and loudly, preventing it from continuing in a corrupt state.

- **Test-Driven Correctness**: Tests are not an afterthought; they are a first-class citizen and a core part of the implementation itself. All components must be developed using a **test-driven development (TDD)** approach. This means writing a failing test _before_ writing the implementation code that makes it pass. This practice forces a clear definition of requirements, ensures every line of code is testable by design, and results in a comprehensive test suite that serves as the ultimate proof of correctness and enables confident, fearless refactoring.

- **Verifiable Truth and No Deception**: All claims about functionality must be backed by demonstrable, verifiable proof. It is strictly forbidden to claim a task is complete or a component is working if it is not. This includes using deceptive placeholders, hardcoded return values that mimic real logic, or any other method that misrepresents the true state of the work. The only acceptable proof of functionality is a comprehensive suite of passing tests that validate the code against its requirements. Trust is paramount, and it is built upon the verifiable truth of the work delivered.

- **Automated Quality Enforcement**: Human review should focus on architectural and logical soundness, not on style or trivial errors. Therefore, the **extensive use of linters, formatters, and static analysis tools is mandatory and non-negotiable.** These tools must be integrated into the development workflow to automatically enforce consistency, catch potential bugs, and maintain a high standard of code quality before any code is submitted for review.

- **Consistency Reduces Cognitive Load**: Follow established style guides and project conventions with rigor. A consistent codebase adheres to the principle of least astonishment, allowing developers to make accurate assumptions about how unfamiliar parts of the system work. This significantly reduces the cognitive load required to understand and contribute to the code.

- **No Premature Optimization**: "Premature optimization is the root of all evil." Do not optimize without data. Write correct, clean, and simple code first. Use profiling tools to identify actual, significant bottlenecks, and only then should you apply targeted optimizations. Unmeasured optimization often adds complexity for negligible real-world gain.

- **Remove What Isn't Used**: Immediately delete any dead code, unused variables, stale files, or unnecessary abstractions. Every line of code is a liability that must be read, understood, and maintained. Unused code clutters the codebase, confuses new developers, and can hide latent bugs.

### Operational Mandates

#### 1\. Pre-Code Validation

- **Confirm Necessity**: Before writing any code, confirm it is absolutely necessary and does not duplicate existing functionality. Duplication leads to wasted effort and creates maintenance nightmares where a bug fixed in one place persists in another. Search the codebase and shared libraries first.

- **Justify New Code**: If new code is required, document why existing solutions are insufficient. This justification forces a critical evaluation of the problem and prevents the codebase from bloating with redundant or unnecessary logic.

#### 2\. Implementation Discipline

- **Declare at the Top**: Instantiate all variables at the top of their respective scope (function or file). This practice makes the inputs and state of a code block immediately obvious to the reader and helps prevent errors from using uninitialized variables.

- **No Magic Numbers**: Never hardcode values directly in the logic. Use named constants or configuration parameters that describe the value's purpose. This improves readability and makes the system easier to modify.

- **Parameterize Everything**: Application logic must be strictly decoupled from configuration. Secrets, API endpoints, feature flags, and environment-specific values must be externalized. This is critical for portability, scalability, and security.

#### 3\. Verification Loop

- The development cycle for any task is a tight, continuous loop: **Test, Implement, Lint, Format, Refactor, Repeat.** This is not a sequence of one-off steps but a fluid process. Adherence to our principle of **Automated Quality Enforcement** is verified at every stage of this loop. Refactoring is not a separate, future task; it is an integral part of implementation, done continuously to maintain code health.

- All code must pass all static analysis, linting, and formatting checks on every change before it can be considered complete. This is a non-negotiable quality gate that prevents trivial errors and enforces consistency automatically.

#### 4\. Security as a Default

- **Least Privilege**: Design systems with secure defaults and operate under the principle of least privilege. Components should only have the permissions they absolutely need to perform their function.

- **Validate at Boundaries**: Rigorously validate, sanitize, and scrutinize all inputs at the boundary of your code. Treat all external data as untrustworthy, whether from a user, an API, or a database.

- **Never Log Secrets**: Secrets (API keys, passwords, personal data) must never be written to logs or stored in plaintext. Accidental exposure in logs is a common and severe security vulnerability.

### Practical Application: An Agent's Workflow Example

To illustrate how these principles translate into action, consider the following task assigned to an agent: **"Create a Python utility that reads a text file, counts the frequency of each word, and writes the results to a CSV file."**

Here is the robust, step-by-step process an agent must follow:

**Step 1: Deconstruct the Request & Validate Necessity**

- **Action:** The agent first applies the **Confirm Necessity** mandate. It searches the existing codebase and shared libraries for any tool related to "word count," "text analysis," or "frequency," ensuring no duplicate work is performed.

- **Outcome:** Finding no such utility, the agent proceeds, noting its validation step. This prevents codebase bloat.

**Step 2: Define the Component Contract via a Failing Test (TDD)**

- **Action:** Adhering to **Test-Driven Correctness**, the agent creates `test_word_counter.py`. It does _not_ write implementation code yet. Instead, it designs the public API through a test.
  - It defines a test for the "happy path." This test creates a temporary input file with the text "hello world hello" and defines the exact expected CSV output: `word,count\nhello,2\nworld,1\n`.

  - The test calls the target function, `word_counter.process_text_file('input.txt', 'output.csv')`, and asserts that the generated output file's content matches the expectation. The function name is clear and follows the **Self-Documenting Code** principle.

- **Outcome:** The agent runs the test, which fails as expected because the function does not yet exist. The requirements are now explicitly defined in executable code.

**Step 3: Minimal Implementation and the Verification Loop**

- **Action:** The agent creates `word_counter.py` and writes the _absolute minimum_ amount of code required to make the failing test pass. This embodies the **Simplicity is Non-Negotiable** principle.

- **Verification Loop:**
  1.  **Test:** The agent runs the test again. It passes. This provides **Verifiable Truth**.

  2.  **Lint/Format:** The agent immediately runs the linter and formatter. This is **Automated Quality Enforcement** in action. The tools may suggest minor style fixes, which are applied.

  3.  **Refactor:** The agent reviews the simple code. It's clean, so no refactoring is needed yet.

- **Outcome:** The agent has a working, tested, and quality-checked implementation for the core functionality.

**Step 4: Increment with More Tests for Edge Cases and Errors**

- **Action:** The agent iterates, expanding test coverage before adding more logic.
  - **Error Handling:** What if the input file doesn't exist? The agent writes a new test that asserts a `FileNotFoundError` is raised. This test fails. The agent adds the file-check logic to the implementation, and the test passes. This follows **Error Handling Excellence**.

  - **Business Logic:** How should punctuation and capitalization be handled? The agent clarifies the requirement (e.g., case-insensitive, punctuation-stripped) and writes a new test with input like "Hello, world! HELLO?". The test asserts the output is still `hello,2` and `world,1`. This test fails. The agent refactors the implementation to add `.lower()` and punctuation removal. All tests are run again and pass.

- **Outcome:** The utility is now robust, handling both expected variations and error conditions correctly. Each piece of logic was added only after a failing test demanded it.

**Step 5: Final Review and Cleanup**

- **Action:** The agent performs a final review against the principles.
  - **Single Responsibility:** The function does one thing. Check.

  - **No Magic Numbers/Parameterize Everything:** The input/output paths are parameters. Check.

  - **Acyclic Dependencies:** The utility has no complex dependencies. Check.

  - **Remove What Isn't Used:** The agent ensures there are no unused imports or variables.

- **Outcome:** The task is complete. The final code is simple, fully tested, automatically quality-checked, and demonstrably correct. It was built methodically, ensuring every line of code has a clear purpose and is validated by a test.
