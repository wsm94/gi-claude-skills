# SPEC-XXX: [Feature Name]

> **Status**: Draft | Ready for Review | Approved | In Progress | Complete
> **Owner**: [Name]
> **Created**: [Date]
> **Last Updated**: [Date]

---

## TIER 1: SPECIFY (What & Why)

---

## 1. Summary

[2-3 sentences describing what this functional area does and why it exists. Should be understandable by non-technical stakeholders.]

## 2. Problem Statement

**Current State**: [How things work now / what's broken / what's missing]

**Desired State**: [How things should work after implementation]

**Impact**: [What happens if we don't solve this? Quantify if possible — cost, time, risk]

## 3. User Stories

### US-1: [Story Title]
**As a** [role/persona]
**I want to** [action/capability]
**So that** [benefit/value]

**Acceptance Criteria**:
- [ ] [Testable condition that proves this works]
- [ ] [Another testable condition]

### US-2: [Story Title]
**As a** [role/persona]
**I want to** [action/capability]
**So that** [benefit/value]

**Acceptance Criteria**:
- [ ] [Testable condition]

## 4. Functional Requirements

### 4.1 Inputs

| Input | Source | Format | Required | Notes |
|-------|--------|--------|----------|-------|
| [Name] | [System/User/File] | [JSON/String/etc.] | Yes/No | [Additional context] |
| | | | | |

### 4.2 Outputs

| Output | Destination | Format | Notes |
|--------|-------------|--------|-------|
| [Name] | [System/API/DB] | [JSON/Event/etc.] | [Additional context] |
| | | | |

### 4.3 Business Rules

| ID | Rule | Notes |
|----|------|-------|
| BR-1 | [Business rule description] | [Edge cases, exceptions] |
| BR-2 | [Business rule description] | |

### 4.4 Validation Rules

| ID | Rule | Error Response |
|----|------|----------------|
| VR-1 | [Validation rule] | [Error code/message] |
| VR-2 | [Validation rule] | |

## 5. Acceptance Criteria

### Scenario 1: [Happy Path Name]
```gherkin
Given [precondition - system state before action]
  And [additional precondition if needed]
When [action - what the user/system does]
Then [outcome - observable result]
  And [additional outcome if needed]
```

### Scenario 2: [Edge Case Name]
```gherkin
Given [precondition]
When [action]
Then [outcome]
```

### Scenario 3: [Error Case Name]
```gherkin
Given [precondition that will cause failure]
When [action]
Then [expected error handling behavior]
```

### Scenario 4: [Another Scenario]
```gherkin
Given [precondition]
When [action]
Then [outcome]
```

## 6. Out of Scope

**Explicitly NOT included in this spec:**
- NOT: [Feature/capability that might be assumed but isn't included]
- NOT: [Another exclusion]

**Deferred to future iterations:**
- FUTURE: [Feature planned for later]
- FUTURE: [Another deferred item]

## 7. Dependencies

| Dependency | Type | Status | Notes |
|------------|------|--------|-------|
| [SPEC-XXX: Other Feature] | Internal | Ready/In Progress/Blocked | [Impact if not ready] |
| [External Service/API] | External | Available/Pending | [Integration details] |

## 8. Success Metrics

| Metric | Target | Measurement Method |
|--------|--------|-------------------|
| [Metric name] | [Quantified target] | [How to measure] |
| [Metric name] | [Quantified target] | [How to measure] |

---

## TIER 2: PLAN (How)

---

## 9. Architecture Overview

### 9.1 Component Diagram

```
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│   Component A   │────▶│   Component B    │────▶│   Component C   │
│                 │     │   [This Spec]    │     │                 │
└─────────────────┘     └────────┬─────────┘     └─────────────────┘
                                 │
                                 ▼
                        ┌──────────────────┐
                        │   Component D    │
                        └──────────────────┘
```

### 9.2 Integration Points

| Component | Integrates With | Method | Direction | Notes |
|-----------|-----------------|--------|-----------|-------|
| [This component] | [Other component] | [REST/gRPC/Event/DB] | In/Out/Both | [Sync/Async, latency] |
| | | | | |

## 10. Data Model

### 10.1 New Entities

```sql
-- Table: [table_name]
-- Purpose: [What this table stores]

CREATE TABLE [table_name] (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    [column_name]   [TYPE] [CONSTRAINTS],  -- [Description]
    [column_name]   [TYPE] [CONSTRAINTS],  -- [Description]
    created_at      TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_[table]_[column] ON [table_name]([column]);

-- Foreign Keys
ALTER TABLE [table_name] 
    ADD CONSTRAINT fk_[name] 
    FOREIGN KEY ([column]) REFERENCES [other_table]([column]);
```

### 10.2 Modified Entities

| Table | Change | Migration Notes |
|-------|--------|-----------------|
| [existing_table] | Add column [name]: [type] | [Default value, nullable] |

### 10.3 Data Flow

```
1. [Source] emits/sends [data type]
   ↓
2. [Component] receives and validates
   ↓
3. [Component] transforms/processes
   ↓
4. [Component] stores in [destination]
   ↓
5. [Component] emits [event/response] to [consumer]
```

## 11. API Design

### Endpoint: [METHOD] [/api/v1/path]

**Purpose**: [What this endpoint does]

**Authentication**: [Required/Optional, method]

**Request Headers**:
```
Content-Type: application/json
Authorization: Bearer <token>
```

**Request Body**:
```json
{
  "field_name": "type — description (required/optional)",
  "nested_object": {
    "sub_field": "type — description"
  }
}
```

**Response: 200 OK**
```json
{
  "field_name": "type — description",
  "metadata": {
    "processed_at": "ISO timestamp"
  }
}
```

**Response: 400 Bad Request**
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Human readable message",
    "details": {
      "field": "field_name",
      "issue": "specific problem"
    }
  }
}
```

**Status Codes**:
| Code | Condition |
|------|-----------|
| 200 | Success |
| 400 | Invalid request body |
| 401 | Missing/invalid authentication |
| 404 | Resource not found |
| 422 | Business rule violation |
| 500 | Internal server error |

---

### Endpoint: [METHOD] [/api/v1/another-path]

[Repeat structure for each endpoint]

---

## 12. Algorithm/Logic Design

### 12.1 [Algorithm Name]

**Purpose**: [What this algorithm calculates/determines]

**Inputs**:
- `param1`: [type] — [description]
- `param2`: [type] — [description]

**Outputs**:
- [type] — [description of what's returned]

**Pseudocode**:
```
function algorithmName(param1, param2):
    // Step 1: [Description]
    intermediate = process(param1)
    
    // Step 2: [Description]
    if condition:
        result = pathA(intermediate)
    else:
        result = pathB(intermediate)
    
    // Step 3: [Description]
    return finalize(result)
```

**Complexity**: O(n) where n = [description of n]

**Edge Cases**:
- When [condition]: [behavior]
- When [condition]: [behavior]

### 12.2 [Next Algorithm]

[Repeat structure]

## 13. Error Handling Strategy

| Error Condition | Error Code | HTTP Status | User Message | Recovery Action |
|-----------------|------------|-------------|--------------|-----------------|
| [Condition] | [CODE] | [4xx/5xx] | [Message] | [What system/user should do] |
| | | | | |

### Retry Strategy

| Error Type | Retry | Max Attempts | Backoff |
|------------|-------|--------------|---------|
| Network timeout | Yes | 3 | Exponential (1s, 2s, 4s) |
| Rate limit | Yes | 5 | Use Retry-After header |
| Validation error | No | - | - |

## 14. Performance Requirements

| Metric | Target | P50 | P95 | P99 | Measurement |
|--------|--------|-----|-----|-----|-------------|
| Latency | <100ms | 50ms | 100ms | 200ms | APM tracing |
| Throughput | 1000 req/s | - | - | - | Load testing |
| Memory | <512MB | - | - | - | Container metrics |
| CPU | <50% | - | - | - | Container metrics |

## 15. Security Considerations

**Authentication**:
- [Method: JWT/API Key/OAuth]
- [Token validation approach]

**Authorization**:
- [Permission model]
- [Role-based access if applicable]

**Data Sensitivity**:
- [ ] Contains PII: [Yes/No — which fields]
- [ ] Contains PHI: [Yes/No — which fields]
- [ ] Encryption requirements: [At rest/In transit]

**Audit Logging**:
- [What actions are logged]
- [Retention policy]

## 16. Testing Strategy

### 16.1 Unit Tests

| Component | Test Focus | Mocking Strategy |
|-----------|------------|------------------|
| [Component] | [What to verify] | [What to mock] |
| | | |

### 16.2 Integration Tests

| Test | Components Involved | Setup Required |
|------|---------------------|----------------|
| [Test name] | [Components] | [DB seed, external mocks] |
| | | |

### 16.3 Edge Cases to Test

**CRITICAL: AI will NOT infer these. List explicitly.**

| # | Edge Case | Expected Behavior | Priority |
|---|-----------|-------------------|----------|
| 1 | Empty input | [Behavior] | High |
| 2 | Null vs undefined | [Behavior] | High |
| 3 | Maximum size input | [Behavior] | Medium |
| 4 | Unicode/special characters | [Behavior] | Medium |
| 5 | Concurrent requests | [Behavior] | High |
| 6 | Partial failure in batch | [Behavior] | High |
| 7 | [Specific to this feature] | [Behavior] | |

## 17. Technical Decisions

### Decision 1: [Decision Title]

**Context**: [Why this decision was needed]

**Options Considered**:

| Option | Pros | Cons |
|--------|------|------|
| A: [Description] | [Pros] | [Cons] |
| B: [Description] | [Pros] | [Cons] |

**Decision**: [Which option and why]

**Consequences**: [What this means for implementation, maintenance, future]

### Decision 2: [Decision Title]

[Repeat structure]

## 18. File Structure

```
src/
├── services/
│   └── [feature]/
│       ├── __init__.py
│       ├── service.py          # Main service class
│       ├── models.py           # Pydantic/data models
│       ├── repository.py       # Data access
│       └── [submodule]/        # Sub-components
│           ├── __init__.py
│           └── [component].py
├── api/
│   └── v1/
│       └── [feature].py        # API endpoints
└── tests/
    └── services/
        └── [feature]/
            ├── test_service.py
            ├── test_models.py
            └── conftest.py     # Fixtures
```

---

## TIER 3: TASKS (Build Steps)

---

## 19. Task Sequence

### Pre-Implementation Checklist

- [ ] Database migrations reviewed and approved
- [ ] API contracts agreed with consumers
- [ ] Test fixtures and mocks available
- [ ] CI pipeline configured for new test paths
- [ ] Feature flag created (if applicable)

### Task Overview

| Task | Description | Test File | Size | Dependencies |
|------|-------------|-----------|------|--------------|
| T1 | [Description] | test_[x].py | S | - |
| T2 | [Description] | test_[y].py | M | T1 |
| T3 | [Description] | test_[z].py | M | T1 |
| T4 | [Description] | test_[w].py | L | T2, T3 |

**Size Guide**: S = <2 hrs, M = 2-4 hrs, L = 4-8 hrs

---

### TASK T1: [Task Name]

**Objective**: [Single sentence describing what this task accomplishes]

**Prerequisites**:
- [What must exist before starting this task]
- [Required config, dependencies, etc.]

**Files to Create/Modify**:
| File | Action | Purpose |
|------|--------|---------|
| `path/to/new_file.py` | Create | [Purpose] |
| `path/to/existing.py` | Modify | [What to change] |

**Implementation Steps**:
1. [Specific, actionable step]
2. [Specific, actionable step]
3. [Specific, actionable step]

**Test Specification**:
```python
# File: tests/path/to/test_file.py

import pytest
from src.path.to.module import ComponentUnderTest

class TestComponentUnderTest:
    
    @pytest.fixture
    def component(self):
        """Setup for tests"""
        return ComponentUnderTest()
    
    def test_[specific_behavior](self, component):
        """
        Given: [Preconditions/Setup]
        When: [Action taken]
        Then: [Expected outcome]
        """
        # Arrange
        input_data = {...}
        
        # Act
        result = component.method(input_data)
        
        # Assert
        assert result.field == expected_value
        assert result.other_field is not None
    
    def test_[edge_case](self, component):
        """
        Given: [Edge case setup]
        When: [Action]
        Then: [Expected handling]
        """
        # Arrange
        edge_input = {...}
        
        # Act & Assert
        with pytest.raises(ExpectedException):
            component.method(edge_input)
```

**Verification Command**:
```bash
pytest tests/path/to/test_file.py -v
```

**Definition of Done**:
- [ ] All tests pass
- [ ] Code follows project conventions (linting passes)
- [ ] No new warnings introduced
- [ ] Type hints complete (mypy passes)
- [ ] Docstrings on public methods

**Notes for AI**:
- [Specific patterns to follow — reference existing code]
- [Common gotchas to avoid]
- [Dependencies to inject vs instantiate]

---

### TASK T2: [Task Name]

**Objective**: [Single sentence]

**Prerequisites**:
- T1 complete
- [Other prerequisites]

**Files to Create/Modify**:
| File | Action | Purpose |
|------|--------|---------|
| | | |

**Implementation Steps**:
1. [Step]
2. [Step]

**Test Specification**:
```python
# File: tests/path/to/test_file.py

class TestFeature:
    
    def test_[behavior](self):
        """
        Given: [Setup]
        When: [Action]
        Then: [Outcome]
        """
        pass
```

**Verification Command**:
```bash
pytest tests/path/to/test_file.py::TestFeature -v
```

**Definition of Done**:
- [ ] Tests pass
- [ ] [Other criteria]

**Notes for AI**:
- [Guidance]

---

### TASK T3: [Continue pattern for remaining tasks]

---

## Appendix

### A. Glossary

| Term | Definition |
|------|------------|
| [Term] | [Definition in context of this feature] |

### B. References

- [Link to related documentation]
- [Link to external API docs]
- [Link to design mockups]

### C. Change Log

| Date | Author | Change |
|------|--------|--------|
| [Date] | [Name] | Initial draft |
| | | |
