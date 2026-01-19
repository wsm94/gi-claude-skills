# SPEC-XXX: [Feature Name]

> **Status**: Draft | Approved | Complete
> **Owner**: [Name]

---

## Summary

[2-3 sentences: what it does and why]

---

## Acceptance Criteria

### Scenario 1: [Happy Path]
```gherkin
Given [precondition]
When [action]
Then [outcome]
```

### Scenario 2: [Edge Case]
```gherkin
Given [precondition]
When [action]
Then [outcome]
```

### Scenario 3: [Error Case]
```gherkin
Given [error condition]
When [action]
Then [error handling]
```

---

## API Design

### [METHOD] [/api/v1/path]

**Request**:
```json
{
  "field": "type — description"
}
```

**Response 200**:
```json
{
  "field": "type — description"
}
```

**Response 4xx**:
```json
{
  "error": {"code": "ERROR_CODE", "message": "description"}
}
```

---

## Edge Cases (TEST THESE)

| Case | Input | Expected |
|------|-------|----------|
| Empty input | `{}` | Return error X |
| Null value | `{"field": null}` | [Behavior] |
| Max size | [Large input] | [Behavior] |
| [Domain-specific] | [Input] | [Behavior] |

---

## Tasks

### T1: [Task Name]
**Test**: [What to verify]
**Verify**: `pytest tests/path/test.py::test_name -v`

```python
def test_[behavior]():
    """Given [X], When [Y], Then [Z]"""
    # Arrange
    # Act  
    # Assert
```

---

### T2: [Task Name]
**Test**: [What to verify]
**Verify**: `pytest tests/path/test.py::test_name -v`

```python
def test_[behavior]():
    """Given [X], When [Y], Then [Z]"""
    pass
```

---

### T3: [Task Name]
**Test**: [What to verify]
**Verify**: `pytest tests/path/test.py::test_name -v`

```python
def test_[behavior]():
    """Given [X], When [Y], Then [Z]"""
    pass
```

---

## Notes for AI

- Follow patterns in `src/[reference_path]/`
- Use [specific library] for [specific task]
- [Gotcha to avoid]
