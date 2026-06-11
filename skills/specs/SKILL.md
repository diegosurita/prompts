---
name: specs
description: 'Create or update a specification file for the solution, optimized for Generative AI consumption, and define the tasks to follow when implementing a specification. Use when asked to create, update, or implement a spec.'
---

# Specifications

Use this skill to create a new specification, update an existing one, or implement what a specification defines.

- **Create**: create a new specification file for `${input:SpecPurpose}`.
- **Update**: update the existing specification file `${file}` based on new requirements or updates to any existing code.
- **Implement**: follow the tasks in [Implementing a Specification](#implementing-a-specification).

The specification file must define the requirements, constraints, and interfaces for the solution components in a manner that is clear, unambiguous, and structured for effective use by Generative AIs. Follow established documentation standards and ensure the content is machine-readable and self-contained.

## Best Practices for AI-Ready Specifications

- Use precise, explicit, and unambiguous language.
- Clearly distinguish between requirements, constraints, and recommendations.
- Use structured formatting (headings, lists, tables) for easy parsing.
- Avoid idioms, metaphors, or context-dependent references.
- Define all acronyms and domain-specific terms.
- Include examples and edge cases where applicable.
- Ensure the document is self-contained and does not rely on external context.

## File Location & Naming

- Save specifications in the [.specs](/.specs/) directory.
- Name files according to the convention `[a-z0-9-]+.md`, where the name should be descriptive of the specification's content and starting with the highlevel purpose, which is one of [schema, tool, data, infrastructure, process, architecture, or design].
- When updating, keep the existing file name and location.
- The specification file must be formatted in well formed Markdown.

## Template

Specification files must follow the template below, ensuring that all sections are filled out appropriately. The front matter for the markdown should be structured correctly as per the example following:

````md
---
title: [Concise Title Describing the Specification's Focus]
version: [Optional: e.g., 1.0, Date]
last_updated: [Optional: YYYY-MM-DD]
---

# Introduction

[A short concise introduction to the specification and the goal it is intended to achieve.]

## 1. Purpose & Scope

[Provide a clear, concise description of the specification's purpose and the scope of its application. State the intended audience and any assumptions.]

## 2. Definitions

[List and define all acronyms, abbreviations, and domain-specific terms used in this specification.]

## 3. Requirements, Constraints & Guidelines

[Explicitly list all requirements, constraints, rules, and guidelines. Use bullet points or tables for clarity.]

- **REQ-001**: Requirement 1
- **SEC-001**: Security Requirement 1
- **[3 LETTERS]-001**: Other Requirement 1
- **CON-001**: Constraint 1
- **GUD-001**: Guideline 1
- **PAT-001**: Pattern to follow 1

## 4. Interfaces & Data Contracts

[Describe the interfaces, APIs, data contracts, or integration points. Use tables or code blocks for schemas and examples.]

## 5. Acceptance Criteria

[Define clear, testable acceptance criteria for each requirement using Given-When-Then format where appropriate.]

- **AC-001**: Given [context], When [action], Then [expected outcome]
- **AC-002**: The system shall [specific behavior] when [condition]
- **AC-003**: [Additional acceptance criteria as needed]

## 6. Test Automation Strategy

[Define the testing approach, frameworks, and automation requirements.]

- **Test Levels**: Unit, Integration, End-to-End
- **Frameworks**: MSTest, FluentAssertions, Moq (for .NET applications)
- **Test Data Management**: [approach for test data creation and cleanup]
- **CI/CD Integration**: [automated testing in GitHub Actions pipelines]
- **Coverage Requirements**: [minimum code coverage thresholds]
- **Performance Testing**: [approach for load and performance testing]

## 7. Rationale & Context

[Explain the reasoning behind the requirements, constraints, and guidelines. Provide context for design decisions.]

## 8. Dependencies & External Integrations

[Define the external systems, services, and architectural dependencies required for this specification. Focus on **what** is needed rather than **how** it's implemented. Avoid specific package or library versions unless they represent architectural constraints.]

### External Systems
- **EXT-001**: [External system name] - [Purpose and integration type]

### Third-Party Services
- **SVC-001**: [Service name] - [Required capabilities and SLA requirements]

### Infrastructure Dependencies
- **INF-001**: [Infrastructure component] - [Requirements and constraints]

### Data Dependencies
- **DAT-001**: [External data source] - [Format, frequency, and access requirements]

### Technology Platform Dependencies
- **PLT-001**: [Platform/runtime requirement] - [Version constraints and rationale]

### Compliance Dependencies
- **COM-001**: [Regulatory or compliance requirement] - [Impact on implementation]

**Note**: This section should focus on architectural and business dependencies, not specific package implementations. For example, specify "OAuth 2.0 authentication library" rather than "Microsoft.AspNetCore.Authentication.JwtBearer v6.0.1".

## 9. Examples & Edge Cases

```code
// Code snippet or data example demonstrating the correct application of the guidelines, including edge cases
```

## 10. Validation Criteria

[List the criteria or tests that must be satisfied for compliance with this specification.]

## 11. Related Specifications / Further Reading

[Link to related spec 1]
[Link to relevant external documentation]

## 12. Implementation Tasks

[Break the implementation into ordered tasks, each linked to the requirement IDs it covers. Update the Status column as the implementation progresses. Allowed statuses: `pending`, `in-progress`, `completed`, `blocked`.]

| ID       | Task                              | Related Requirements | Status  |
|----------|-----------------------------------|----------------------|---------|
| TASK-001 | [Implementation task description] | REQ-001, SEC-001     | pending |
| TASK-002 | [Implementation task description] | REQ-002              | pending |

````

## Implementing a Specification

When asked to implement a specification, follow these tasks in order:

1. **Read the entire specification** before writing any code.
2. **Map requirements to code**: list every ID from section 3 (REQ, SEC, CON, GUD, PAT) and identify the files, classes, and migrations each one affects.
3. **Resolve ambiguities first**: if a requirement is unclear, contradictory, or conflicts with existing code, ask before implementing — never guess.
4. **Record the tasks in the spec**: break the implementation into ordered tasks and add them to Implementation Tasks (section 12) of the specification file, each linked to the requirement IDs it covers and with status `pending`. Add the section when the spec does not have it yet.
5. **Track status in the spec**: as work progresses, update each task to `in-progress` when started, `completed` when finished, or `blocked` with the reason.
6. **Implement to the spec, nothing more**: satisfy every requirement and constraint, follow the declared patterns, and do not add behavior the specification does not define.
7. **Honor Interfaces & Data Contracts (section 4) exactly**: endpoints, payloads, schemas, and signatures must match the spec.
8. **Cover the documented edge cases** from Examples & Edge Cases (section 9).
9. **Write tests per the Test Automation Strategy (section 6)**, asserting every Acceptance Criterion from section 5.
10. **Check the Validation Criteria (section 10)**: confirm every item passes and every task in section 12 is `completed` before considering the implementation done.
11. **Keep the spec in sync**: if the implementation deviates from the specification, update the specification file in the same change.
