```markdown
# AGENTS.md - Guidelines for AI Coding Agents

These guidelines outline the best practices for developing AI coding agents. Adherence to these principles is crucial for maintaining a sustainable, scalable, and reliable codebase.

## 1. DRY (Don't Repeat Yourself)

*   All functions, classes, and modules should have a single, well-defined purpose.
*   Avoid duplicating logic. Implement reusable components and abstractions.
*   When necessary, leverage existing libraries and frameworks to reduce boilerplate.

## 2. KISS (Keep It Simple, Stupid)

*   Prioritize clarity and readability above all else.
*   Keep functions and classes concise and easy to understand.
*   Avoid unnecessary complexity.
*   Favor straightforward solutions over overly clever ones.

## 3. SOLID Principles

*   **Single Responsibility Principle:** Each class should have one and only one reason to change.
*   **Open/Closed Principle:**  The system should be extensible without modifying the core logic.
*   **Liskov Substitution Principle:**  Subclasses should be substitutable for their base classes without altering the correctness of the program.
*   **Interface Segregation Principle:** Each interface should have a specific, single, and stable reason for needing it.
*   **Dependency Inversion Principle:**  High-level modules should not depend on low-level modules.

## 4. YAGNI (You Aren't Gonna Need It)

*   Only implement functionality that is explicitly required for the current task.
*   Refactor only when necessary to improve existing functionality, not because something is missing.
*   Don’t introduce features or code that aren’t currently needed.

## 5. Code Structure and Organization

*   **File Size:** Each file must be less than 180 lines of code.
*   **Naming Conventions:** Use consistent and descriptive naming conventions (e.g., snake_case).  Classes: `Agent.py`, `Model.py`, `Validator.py`. Functions: `process_data(data, threshold)`
*   **Comments:** Add clear, concise comments where necessary to explain complex logic or design decisions.  Don't over-comment.
*   **Modularity:** Break down large modules into smaller, manageable units.
*   **Error Handling:** Implement appropriate error handling to prevent crashes and provide informative error messages.

## 6. Testing

*   **Unit Tests:** All functions and classes must be thoroughly tested with unit tests.
*   **Test Coverage:** Aim for 80% test coverage.  Tools like `coverage.py` should be used.
*   **Test-Driven Development:** Write tests *before* writing code.
*   **Negative Testing:**  Simulate error conditions and ensure code behaves as expected under adverse circumstances.

## 7. Development Process

*   **Version Control:** Use Git for version control.  Follow a standard branching strategy (e.g., Gitflow).
*   **Code Review:**  All code changes should be reviewed by at least one other team member before merging.
*   **Documentation:** Provide clear and concise documentation for all classes, functions, and modules.
*   **Continuous Integration/Continuous Deployment (CI/CD):** Implement a CI/CD pipeline to automate testing and deployment.

## 8. AI-Specific Considerations

*   **Model-Driven Development:** Consider using a model-driven approach to generate code from models.
*   **Data Validation:** Implement robust data validation and integrity checks.
*   **Error Handling:**  Handle errors gracefully and provide informative messages to the user.
*   **State Management:**  Design a clear and manageable state management system.



These guidelines are intended as a starting point.  Adapt and refine these principles as needed throughout the development process.  Regularly revisit and update these documents as the project evolves.
```