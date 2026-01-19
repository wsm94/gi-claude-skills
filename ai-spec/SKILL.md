---
name: ai-spec
description: Create specifications for AI-assisted development that enable test-driven, incremental implementation. Use when building new features, API endpoints, services, or functional areas with AI coding assistants (Cursor, Claude Code, Windsurf, Copilot). Triggers include requests to "write a spec", "create a specification", "plan a feature", "design an API", "break down implementation", or when starting any new functional area that will be built with AI assistance.
---

# AI Spec: Specification-Driven Development

This skill creates specifications that AI coding assistants can translate into tested, working code. The core principle: **tests are the specification** — AI implements code to pass your tests, not tests to validate AI's code.

## Three-Tier Structure

Every functional area gets ONE specification document with three tiers:

| Tier | Purpose | Audience | Key Output |
|------|---------|----------|------------|
| **SPECIFY** | What & Why | Product/Stakeholders | Acceptance criteria in Gherkin |
| **PLAN** | How | Tech Lead/AI | Architecture, API contracts, algorithms |
| **TASKS** | Build Steps | Developer/AI | Testable implementation steps |

## Quick Start

1. Copy template from `assets/spec-template.md`
2. Fill Tier 1 (Specify) → Get stakeholder approval
3. Fill Tier 2 (Plan) → Get technical approval
4. Fill Tier 3 (Tasks) → Execute incrementally

## Tier 1: SPECIFY (What & Why)

Defines functional requirements from product/business perspective. Non-technical stakeholders should understand this.

**Required sections:**

```markdown
# SPEC-XXX: [Feature Name]

## 1. Summary
[2-3 sentences: what it does and why it exists]

## 2. Problem Statement
- Current state: [What's broken/missing]
- Desired state: [How it should work]
- Impact: [Quantified cost of not solving]

## 3. User Stories
US-1: As a [role], I want [action] so that [benefit]
  - Acceptance: [Testable condition]

## 4. Functional Requirements
### 4.1 Inputs
| Input | Source | Format | Required | Notes |
### 4.2 Outputs
| Output | Destination | Format | Notes |
### 4.3 Business Rules
BR-1: [Rule] — BR-2: [Rule]
### 4.4 Validation Rules
VR-1: [Rule] — VR-2: [Rule]

## 5. Acceptance Criteria (Gherkin)
### Scenario 1: [Happy path]
Given [precondition]
When [action]
Then [outcome]

### Scenario 2: [Edge case]
### Scenario 3: [Error case]

## 6. Out of Scope
- NOT: [Explicitly excluded]
- FUTURE: [Deferred to later]

## 7. Dependencies
- Depends on: [Other systems/specs]

## 8. Success Metrics
- [Measurable outcome]
```

## Tier 2: PLAN (How)

Defines technical architecture. AI needs this to understand integration points and patterns.

**Required sections:**

```markdown
---
## TECHNICAL PLAN
---

## 9. Architecture Overview
### 9.1 Component Diagram
[ASCII diagram of components and relationships]
### 9.2 Integration Points
| Component | Integrates With | Method | Notes |

## 10. Data Model
### 10.1 New Entities
[Table definitions with columns, types, indexes]
### 10.2 Data Flow
[Numbered steps of how data moves]

## 11. API Design
### Endpoint: [METHOD] [PATH]
**Request**: [JSON schema]
**Response (Success)**: [JSON schema]
**Response (Error)**: [Error schema]
**Status Codes**: [When each is returned]

## 12. Algorithm/Logic Design
### 12.1 [Algorithm Name]
**Inputs**: **Outputs**: **Pseudocode**: **Complexity**:

## 13. Error Handling
| Condition | Code | Status | Recovery |

## 14. Performance Requirements
| Metric | Target | Measurement |

## 15. Security
[Auth, authorization, data sensitivity, audit]

## 16. Testing Strategy
### 16.1 Unit Tests — ### 16.2 Integration Tests
### 16.3 Edge Cases (EXPLICIT LIST - AI won't infer these)

## 17. Technical Decisions
### Decision: [Title]
**Context**: **Options**: **Decision**: **Consequences**:

## 18. File Structure
[Directory tree showing where code lives]
```

## Tier 3: TASKS (Build Steps)

Breaks implementation into small, independently testable steps. **Each task has ONE test criterion.**

**Required sections:**

```markdown
---
## IMPLEMENTATION TASKS
---

## 19. Task Sequence

### Pre-Implementation Checklist
- [ ] Migrations reviewed
- [ ] API contracts agreed
- [ ] Test fixtures available

### Task Overview
| Task | Description | Test File | Size |

---

### TASK T1: [Name]

**Objective**: [Single sentence]

**Prerequisites**: [What must exist]

**Files to Create/Modify**:
- `path/to/file.py` - [Purpose]

**Implementation Steps**:
1. [Step]
2. [Step]

**Test Specification**:
```python
def test_[behavior]():
    """
    Given: [Setup]
    When: [Action]
    Then: [Outcome]
    """
    # Arrange / Act / Assert
```

**Verification Command**:
```bash
pytest path/to/test.py::test_[behavior] -v
```

**Definition of Done**:
- [ ] Test passes
- [ ] Linting passes
- [ ] No new warnings

**Notes for AI**:
- [Gotchas, patterns, references to similar code]
```

## Critical Principles

### Tests Define Behavior
Write test specifications BEFORE implementation. AI writes code to pass tests, not tests to validate code. This inverts the failure mode where AI generates plausible-but-broken code.

### Edge Cases Must Be Explicit
AI will NOT infer edge cases. List them explicitly in:
- Tier 1: Acceptance criteria scenarios
- Tier 2: Section 16.3 edge case list
- Tier 3: Test specifications per task

### One Task = One Test
Each task should be verifiable with a single test run. If a task needs multiple unrelated tests, split it.

### Context Placement Matters
Place critical information at the **beginning** or **end** of sections. Information in the middle of long content gets "lost" by AI models.

## Workflow with AI Assistants

When implementing, reference specs like this:

```
@spec-001 Implement TASK T2: Pattern Match Calculator

Follow the test specification exactly. Run verification command after.
```

The AI has everything in one document: business context (T1), technical design (T2), exact steps (T3), and test specifications.

## Templates and References

- **Full template**: See `assets/spec-template.md`
- **Condensed template**: See `assets/spec-template-minimal.md`
- **Example spec**: See `references/example-confidence-scoring.md`
- **Gherkin guide**: See `references/gherkin-patterns.md`

## File Naming Convention

```
specs/
├── SPEC-001-[feature-name].md
├── SPEC-002-[feature-name].md
└── ...
```

Use sequential numbering. One file per functional area.
