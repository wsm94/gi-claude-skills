# Gherkin Patterns for AI-Consumable Specifications

Gherkin (Given/When/Then) syntax translates exceptionally well to AI consumption because it provides unambiguous acceptance criteria that map directly to test cases.

## Core Structure

```gherkin
Given [precondition — system state before action]
  And [additional precondition]
When [action — what triggers the behavior]
Then [outcome — observable, testable result]
  And [additional outcome]
```

## Writing Effective Scenarios

### Be Specific, Not Vague

❌ **Bad**: 
```gherkin
Given a user
When they submit a form
Then it should work
```

✅ **Good**:
```gherkin
Given a registered user with email "test@example.com"
  And the user has not exceeded their daily API limit
When the user submits a confidence score request for record "rec_123"
Then the response status code is 200
  And the response contains a "confidence_score" between 0.0 and 1.0
  And the response contains a "calculated_at" timestamp
```

### Include Concrete Values

❌ **Bad**:
```gherkin
Given some data
When processed
Then confidence is calculated
```

✅ **Good**:
```gherkin
Given a data record with fields:
  | field_name | value      | required |
  | name       | "Test Co"  | yes      |
  | amount     | 1500.00    | yes      |
  | notes      | null       | no       |
When the confidence scoring engine processes the record
Then the confidence score is >= 0.85
  And the "validation_pass" dimension scores 1.0
  And no quality flags are present
```

### Use Tables for Multiple Inputs/Outputs

```gherkin
Scenario Outline: Confidence score reflects data completeness
Given a record with <complete_fields> of 10 required fields populated
When the confidence engine calculates the score
Then the confidence score is approximately <expected_score>
  And the quality flags include <expected_flags>

Examples:
  | complete_fields | expected_score | expected_flags          |
  | 10              | 0.95           | []                      |
  | 8               | 0.75           | ["incomplete"]          |
  | 5               | 0.50           | ["incomplete", "review"]|
  | 0               | 0.00           | ["empty", "reject"]     |
```

## Scenario Categories

### 1. Happy Path
The primary success case. Should always be Scenario 1.

```gherkin
Scenario: Successfully calculate confidence for complete record
Given a transformed record with all required fields populated
  And the source profile matches a known template
  And historical rules exist for similar patterns
When the confidence scoring engine processes the record
Then the overall confidence score is >= 0.85
  And the review decision is "AUTO_APPROVE"
  And processing completes in under 100ms
```

### 2. Edge Cases
Boundary conditions and unusual-but-valid inputs.

```gherkin
Scenario: Handle maximum batch size
Given a batch request containing exactly 10,000 records
When the confidence engine processes the batch
Then all 10,000 records receive scores
  And processing completes in under 60 seconds
  And no timeout errors occur

Scenario: Handle unicode in field values
Given a record with field values containing unicode characters "日本語テスト"
When the confidence engine processes the record
Then the pattern matching dimension completes without error
  And the score reflects the actual pattern match quality

Scenario: Process record at exact threshold boundary
Given a record with weighted dimension scores summing to exactly 0.850000
When the confidence engine calculates the overall score
Then the confidence level is "HIGH" (not "VERY_HIGH")
  And the review decision is "AUTO_APPROVE"
```

### 3. Error Cases
Invalid inputs and failure scenarios.

```gherkin
Scenario: Reject request with missing required field
Given a confidence request missing the "schema_id" field
When the API receives the request
Then the response status code is 400
  And the error code is "VALIDATION_ERROR"
  And the error details identify "schema_id" as missing

Scenario: Handle schema not found
Given a confidence request with schema_id "nonexistent-schema"
When the API receives the request
Then the response status code is 404
  And the error code is "SCHEMA_NOT_FOUND"

Scenario: Graceful degradation when historical store unavailable
Given the historical rules database is unreachable
When the confidence engine processes a record
Then the historical_support dimension scores 0.0
  And the overall score still calculates (not error)
  And quality flags include "historical_unavailable"
```

### 4. Security/Authorization Cases

```gherkin
Scenario: Reject unauthenticated request
Given a request without an Authorization header
When the API receives the request
Then the response status code is 401
  And no confidence calculation occurs

Scenario: Prevent cross-tenant data access
Given a user authenticated for tenant "tenant_a"
When the user requests confidence for a record belonging to "tenant_b"
Then the response status code is 404
  And the audit log records an access attempt
```

### 5. Performance/Timing Cases

```gherkin
Scenario: Meet latency SLA for single record
Given a single record request
When the confidence engine processes the record
Then the total request duration is under 100ms (P95)

Scenario: Apply timeout for long-running calculation
Given a record that triggers expensive semantic analysis
  And the analysis takes longer than 500ms
When the confidence engine processes the record
Then the semantic_coherence dimension returns a partial score
  And quality flags include "semantic_timeout"
  And total processing does not exceed 1 second
```

## Translating Gherkin to Tests

Each scenario maps directly to a test function:

**Gherkin**:
```gherkin
Scenario: Missing required field reduces confidence
Given a record missing the required "source_id" field
When the confidence engine processes the record
Then the validation_pass dimension scores 0.0
  And the overall confidence is <= 0.5
  And quality flags include "missing_required_field"
```

**Test**:
```python
def test_missing_required_field_reduces_confidence():
    """
    Given: A record missing the required "source_id" field
    When: The confidence engine processes the record
    Then: Validation fails, confidence capped, flag added
    """
    # Arrange
    record = {"name": "Test", "amount": 100}  # Missing source_id
    schema = Schema(required_fields=["source_id", "name", "amount"])
    engine = ConfidenceEngine()
    
    # Act
    result = engine.calculate(record, schema)
    
    # Assert
    assert result.dimensions["validation_pass"].score == 0.0
    assert result.overall_score <= 0.5
    assert "missing_required_field" in result.quality_flags
```

## Common Patterns for Data Platforms

### Transformation Scenarios
```gherkin
Given a source file in format [FORMAT] with [N] rows
  And a target schema expecting [FIELDS]
When the transformation engine processes the file
Then [N] transformed records are produced
  And each record validates against the target schema
```

### Validation Scenarios
```gherkin
Given a record with [FIELD] set to [INVALID_VALUE]
When validation rules are applied
Then the validation result includes error code [CODE]
  And the error message describes [ISSUE]
```

### Batch Processing Scenarios
```gherkin
Given a batch of [N] records
  And [X]% have high confidence (>0.85)
  And [Y]% have medium confidence (0.5-0.85)
  And [Z]% have low confidence (<0.5)
When the batch completes processing
Then [X]% are routed to "AUTO_APPROVE"
  And [Y+Z]% are routed to "REVIEW"
```

### Historical Learning Scenarios
```gherkin
Given a transformation pattern that has been approved [N] times
When a new record matches this pattern
Then the historical_support dimension scores >= [THRESHOLD]
  And the matched rule ID is included in the trace
```

## Anti-Patterns to Avoid

### Don't Test Implementation Details
❌ **Bad**:
```gherkin
Then the _calculate_weighted_average method is called
  And the Redis cache is updated
```

✅ **Good**:
```gherkin
Then the confidence score reflects the weighted dimensions
  And subsequent identical requests return cached results
```

### Don't Use Vague Assertions
❌ **Bad**:
```gherkin
Then the system handles it correctly
Then an appropriate error is returned
Then the user sees the right thing
```

✅ **Good**:
```gherkin
Then the response status code is 422
Then the error code is "BUSINESS_RULE_VIOLATION"
Then the response includes field "confidence_score" with type number
```

### Don't Combine Unrelated Behaviors
❌ **Bad**:
```gherkin
Scenario: Process record and send notification and update cache
```

✅ **Good**: Split into three focused scenarios.
