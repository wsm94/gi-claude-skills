# SPEC-001: Confidence Scoring Engine

> **Status**: Approved
> **Owner**: Platform Team
> **Created**: 2024-01-15

---

## TIER 1: SPECIFY (What & Why)

---

## 1. Summary

The Confidence Scoring Engine calculates a multi-dimensional confidence score (0.0-1.0) for each transformed data record. This score determines whether records are auto-approved, flagged for review, or rejected, enabling the human-in-the-loop workflow that balances automation with accuracy for high-volume data transformation operations.

## 2. Problem Statement

**Current State**: All transformed records require manual review, creating a bottleneck that scales linearly with volume. Processing 10,000 records/day requires 8+ FTE reviewers at ~3 minutes per record.

**Desired State**: High-confidence records (>0.85) auto-approve without human intervention. Only low-confidence records (<0.70) require human review. Medium-confidence records are spot-checked via sampling.

**Impact**: Without confidence scoring:
- Labor cost: $400k/year for review team at current volume
- Latency: 24-48 hour processing delays
- Scale ceiling: Cannot grow beyond reviewer capacity

## 3. User Stories

### US-1: Understand Low Confidence Reasons
**As a** data operator reviewing flagged records
**I want to** see exactly why a record has low confidence
**So that** I can fix the specific issue rather than reviewing everything

**Acceptance Criteria**:
- [ ] Each score includes breakdown by all 6 dimensions
- [ ] Each dimension includes explanation text
- [ ] Limiting factors (dimensions <0.5) are highlighted

### US-2: Configure Thresholds Per Schema
**As a** system administrator
**I want to** configure confidence thresholds differently for each schema
**So that** high-risk data types can require stricter review

**Acceptance Criteria**:
- [ ] Thresholds configurable per schema
- [ ] Changes take effect on next job (no restart required)
- [ ] Default thresholds apply when not configured

### US-3: Audit Confidence Calculations
**As a** compliance auditor
**I want to** see the complete calculation methodology for any score
**So that** I can verify the system is working correctly

**Acceptance Criteria**:
- [ ] Full calculation trace stored for every record
- [ ] Trace includes all inputs, weights, and intermediate values
- [ ] Traces retrievable for 7 years per retention policy

## 4. Functional Requirements

### 4.1 Inputs

| Input | Source | Format | Required | Notes |
|-------|--------|--------|----------|-------|
| Transformed record | Transformation Engine | JSON object | Yes | Complete record after transformation |
| Schema definition | Schema Library | JSON Schema | Yes | Field definitions, types, requirements |
| Source metadata | File Ingestion | JSON object | No | Original file info, source system ID |
| Historical rules | Learning Store | Array of Rule | No | Previously approved transformation patterns |
| Source profile | Source Profile Store | Hash + metadata | No | Known source fingerprints |

### 4.2 Outputs

| Output | Destination | Format | Notes |
|--------|-------------|--------|-------|
| Confidence result | Job Record | ConfidenceResult JSON | Score, dimensions, flags, trace |
| Review decision | Workflow Router | Enum | AUTO_APPROVE, REVIEW, REJECT |
| Audit event | Audit Log | Event | Immutable calculation record |

### 4.3 Business Rules

| ID | Rule | Notes |
|----|------|-------|
| BR-1 | Overall confidence = weighted average of 6 dimensions | Weights configurable per schema |
| BR-2 | If ANY dimension = 0.0, overall confidence ≤ 0.5 | Zero indicates blocking failure |
| BR-3 | Historical support weight increases with approval count | Max 2.7x multiplier at 100+ approvals |
| BR-4 | Source profile mismatch for generic codes caps confidence at 0.3 | Prevents wrong code mapping |
| BR-5 | Validation failures with severity="blocking" force confidence = 0.0 | Hard validation enforcement |

### 4.4 Validation Rules

| ID | Rule | Error Response |
|----|------|----------------|
| VR-1 | Confidence score must be 0.0-1.0 inclusive | Internal error if violated |
| VR-2 | All 6 dimensions must be present | INCOMPLETE_DIMENSIONS |
| VR-3 | Dimension weights must sum to 1.0 | INVALID_WEIGHTS |
| VR-4 | Record ID must exist in transformation store | RECORD_NOT_FOUND |

## 5. Acceptance Criteria

### Scenario 1: High Confidence Record Auto-Approves
```gherkin
Given a transformed record with all required fields populated
  And the source profile matches a known template (similarity > 0.95)
  And historical rules show 50+ previous approvals for similar patterns
  And all validation rules pass
When the confidence engine processes the record
Then the overall confidence score is >= 0.85
  And the confidence level is "VERY_HIGH" or "HIGH"
  And the review decision is "AUTO_APPROVE"
  And processing time is < 100ms
```

### Scenario 2: Missing Required Field Caps Confidence
```gherkin
Given a transformed record missing a required field per schema
When the confidence engine processes the record
Then the validation_pass dimension scores 0.0
  And the overall confidence is <= 0.5
  And quality_flags include "missing_required_field"
  And the review decision is "REVIEW"
```

### Scenario 3: Source Profile Mismatch Flags Generic Codes
```gherkin
Given a file containing a generic code field (value_type: "generic_code")
  And the source profile hash does not match any known template
When the confidence engine processes records from this file
Then ALL generic code fields have source_profile dimension <= 0.3
  And quality_flags include "source_profile_mismatch"
  And the review decision is "REVIEW" for all affected records
  And the limiting_factors array identifies source_profile as the issue
```

### Scenario 4: New Pattern With No History
```gherkin
Given a transformation pattern with no historical precedent
  And all other dimensions score >= 0.9
When the confidence engine processes the record
Then historical_support dimension scores 0.0
  And overall confidence is reduced proportionally
  And quality_flags include "new_pattern"
  And the system suggests creating a learned rule upon approval
```

### Scenario 5: Semantic Inconsistency Detected
```gherkin
Given a healthcare claim record where:
  | field | value |
  | patient_age | 5 |
  | diagnosis | "Essential Hypertension" |
When the confidence engine processes the record
Then the semantic_coherence dimension scores < 0.5
  And quality_flags include "semantic_inconsistency"
  And the dimension details explain "Diagnosis unusual for patient age"
```

### Scenario 6: Batch Processing Performance
```gherkin
Given a batch of 10,000 records
When the confidence engine processes the batch
Then all records receive confidence scores
  And total processing time is < 60 seconds
  And 95% of individual records complete in < 100ms
  And no records fail due to timeout
```

## 6. Out of Scope

**Explicitly NOT included**:
- NOT: Modifying transformation results (scoring is read-only)
- NOT: Machine learning model training (uses rule-based scoring)
- NOT: Real-time streaming (batch processing only for MVP)
- NOT: Custom dimension implementations per tenant

**Deferred to future**:
- FUTURE: Adaptive threshold learning based on reviewer feedback
- FUTURE: Custom dimension weights per field type
- FUTURE: Streaming/real-time scoring
- FUTURE: ML-enhanced semantic coherence

## 7. Dependencies

| Dependency | Type | Status | Notes |
|------------|------|--------|-------|
| Schema Library (SPEC-003) | Internal | Ready | Field definitions required |
| Transformation Engine (SPEC-002) | Internal | Ready | Produces input records |
| Learning Store (SPEC-005) | Internal | In Progress | Historical rules |
| Source Profile Store | Internal | In Progress | Profile matching |

## 8. Success Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Auto-approval accuracy | 95%+ of auto-approved records have zero corrections in spot-check | Weekly sampling audit |
| False positive rate | <5% of flagged records are actually correct | Review feedback tracking |
| Processing latency P95 | <100ms per record | APM metrics |
| Calculation reproducibility | 100% (same inputs = same score always) | Regression test suite |
| Reviewer time saved | 75% reduction in review hours | Before/after comparison |

---

## TIER 2: PLAN (How)

---

## 9. Architecture Overview

### 9.1 Component Diagram

```
┌─────────────────┐     ┌──────────────────────────────────────┐
│ Transformation  │────▶│         Confidence Scoring           │
│    Engine       │     │              Engine                  │
└─────────────────┘     │  ┌─────────────────────────────────┐ │
                        │  │      Dimension Calculators      │ │
                        │  │  ┌──────┐ ┌──────┐ ┌──────┐    │ │
                        │  │  │Patt. │ │Hist. │ │Source│    │ │
                        │  │  │Match │ │Supp. │ │Prof. │    │ │
                        │  │  └──────┘ └──────┘ └──────┘    │ │
                        │  │  ┌──────┐ ┌──────┐ ┌──────┐    │ │
                        │  │  │Valid.│ │Seman.│ │Biz   │    │ │
                        │  │  │Pass  │ │Coher.│ │Rules │    │ │
                        │  │  └──────┘ └──────┘ └──────┘    │ │
                        │  └─────────────────────────────────┘ │
                        │  ┌─────────────────────────────────┐ │
                        │  │   Weighted Average Calculator   │ │
                        │  └─────────────────────────────────┘ │
                        └───────────────┬──────────────────────┘
                                        │
           ┌────────────────────────────┼────────────────────────────┐
           ▼                            ▼                            ▼
┌──────────────────┐         ┌──────────────────┐         ┌──────────────────┐
│  Schema Library  │         │  Learning Store  │         │  Source Profiles │
└──────────────────┘         └──────────────────┘         └──────────────────┘
```

### 9.2 Integration Points

| Component | Integrates With | Method | Direction | Latency |
|-----------|-----------------|--------|-----------|---------|
| Confidence Engine | Schema Library | gRPC | In | <10ms |
| Confidence Engine | Learning Store | PostgreSQL | In | <50ms (batched) |
| Confidence Engine | Source Profiles | Redis | In | <5ms |
| Confidence Engine | Workflow Router | Kafka | Out | Async |
| Confidence Engine | Audit Log | Kafka | Out | Async |

## 10. Data Model

### 10.1 New Entities

```sql
-- Table: confidence_scores
-- Purpose: Store calculated confidence for each transformed record

CREATE TABLE confidence_scores (
    id                  UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    record_id           UUID NOT NULL REFERENCES transformed_records(id),
    job_id              UUID NOT NULL REFERENCES jobs(id),
    overall_score       DECIMAL(4,3) NOT NULL CHECK (overall_score >= 0 AND overall_score <= 1),
    confidence_level    VARCHAR(20) NOT NULL CHECK (confidence_level IN ('VERY_HIGH', 'HIGH', 'MEDIUM', 'LOW', 'VERY_LOW')),
    review_decision     VARCHAR(20) NOT NULL CHECK (review_decision IN ('AUTO_APPROVE', 'REVIEW', 'REJECT')),
    dimensions          JSONB NOT NULL,
    quality_flags       TEXT[] NOT NULL DEFAULT '{}',
    limiting_factors    JSONB NOT NULL DEFAULT '[]',
    calculation_trace   JSONB NOT NULL,
    calculated_at       TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT NOW(),
    
    CONSTRAINT unique_record_score UNIQUE (record_id)
);

CREATE INDEX idx_confidence_scores_job ON confidence_scores(job_id);
CREATE INDEX idx_confidence_scores_decision ON confidence_scores(job_id, review_decision);
CREATE INDEX idx_confidence_scores_level ON confidence_scores(job_id, confidence_level);
```

### 10.2 Data Flow

```
1. Transformation Engine completes record → publishes to Kafka topic
   ↓
2. Confidence Engine consumes batch of records
   ↓
3. For each record:
   a. Load schema definition from Schema Library (cached)
   b. Load historical rules for schema (batched, cached)
   c. Calculate each dimension in parallel:
      - Pattern Match (deterministic, <1ms)
      - Historical Support (DB lookup, <5ms)
      - Source Profile (Redis, <2ms)
      - Validation Pass (deterministic, <1ms)
      - Semantic Coherence (AI call if enabled, ~300ms)
      - Business Rules (deterministic, <1ms)
   d. Calculate weighted average
   e. Apply adjustment rules
   f. Generate quality flags
   ↓
4. Batch insert confidence_scores
   ↓
5. Publish routing decisions to Workflow Router
   ↓
6. Publish audit events to Audit Log
```

## 11. API Design

### Endpoint: POST /api/v1/confidence-scores/calculate

**Purpose**: Calculate confidence scores for one or more transformed records

**Authentication**: Service-to-service JWT

**Request**:
```json
{
  "records": [
    {
      "record_id": "uuid — required, must exist in transformed_records",
      "transformed_data": "object — the transformed record data",
      "schema_id": "uuid — required, must exist in schema library",
      "source_metadata": {
        "file_id": "uuid — optional",
        "source_system": "string — optional",
        "row_number": "integer — optional"
      }
    }
  ],
  "options": {
    "include_trace": "boolean — default false",
    "enable_semantic": "boolean — default true",
    "threshold_overrides": {
      "auto_approve": "number — optional, default 0.85",
      "review": "number — optional, default 0.50"
    }
  }
}
```

**Response: 200 OK**
```json
{
  "results": [
    {
      "record_id": "uuid",
      "confidence": {
        "overall_score": 0.87,
        "confidence_level": "HIGH",
        "review_decision": "AUTO_APPROVE",
        "dimensions": {
          "pattern_match": {
            "score": 0.95,
            "weight": 0.25,
            "details": "All fields match expected types"
          },
          "historical_support": {
            "score": 0.80,
            "weight": 0.25,
            "details": "Matched rule R-123 with 47 prior approvals",
            "matched_rule_id": "R-123"
          },
          "source_profile": {
            "score": 1.0,
            "weight": 0.15,
            "details": "Exact match to template T-456"
          },
          "validation_pass": {
            "score": 1.0,
            "weight": 0.20,
            "details": "All validation rules passed"
          },
          "semantic_coherence": {
            "score": 0.70,
            "weight": 0.10,
            "details": "Minor inconsistency flagged",
            "ai_model": "claude-3-opus"
          },
          "business_rules": {
            "score": 1.0,
            "weight": 0.05,
            "details": "All business rules passed"
          }
        },
        "quality_flags": [],
        "limiting_factors": []
      },
      "calculation_trace": {}
    }
  ],
  "summary": {
    "total": 100,
    "auto_approve": 85,
    "review": 12,
    "reject": 3,
    "processing_time_ms": 423
  }
}
```

**Response: 400 Bad Request**
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Request validation failed",
    "details": [
      {"field": "records[0].schema_id", "issue": "Required field missing"}
    ]
  }
}
```

**Status Codes**:
| Code | Condition |
|------|-----------|
| 200 | Scores calculated (even if some records have low confidence) |
| 400 | Invalid request structure |
| 404 | Schema not found |
| 422 | Record not found in transformation store |
| 500 | Internal calculation error |

### Endpoint: GET /api/v1/confidence-scores/{record_id}

**Purpose**: Retrieve confidence score for a specific record

**Response: 200 OK**
```json
{
  "record_id": "uuid",
  "confidence": { ... },
  "calculated_at": "2024-01-15T10:30:00Z"
}
```

## 12. Algorithm/Logic Design

### 12.1 Weighted Average with Adjustments

**Purpose**: Calculate overall confidence from dimension scores

**Inputs**:
- `dimensions`: Array of {name, score, weight}
- `adjustments`: Adjustment rules from schema config

**Outputs**:
- `overall_score`: 0.0-1.0
- `limiting_factors`: Dimensions that reduced score
- `applied_adjustments`: List of adjustments applied

**Pseudocode**:
```
function calculateOverallConfidence(dimensions, adjustments):
    // Step 1: Raw weighted average
    weighted_sum = sum(d.score * d.weight for d in dimensions)
    weight_sum = sum(d.weight for d in dimensions)
    raw_score = weighted_sum / weight_sum
    
    // Step 2: Identify limiting factors
    limiting = [d for d in dimensions if d.score < 0.5]
    
    // Step 3: Apply adjustment rules
    adjusted = raw_score
    applied = []
    
    if any(d.score == 0.0 for d in dimensions):
        adjusted = min(adjusted, 0.5)
        applied.append("zero_dimension_cap")
    
    if dimensions["source_profile"].score < 0.3 and has_generic_codes:
        adjusted = min(adjusted, 0.3)
        applied.append("source_profile_cap")
    
    // Step 4: Floor at 0.0
    final = max(adjusted, 0.0)
    
    return {
        overall_score: round(final, 3),
        raw_score: round(raw_score, 3),
        limiting_factors: limiting,
        applied_adjustments: applied
    }
```

**Complexity**: O(1) — fixed 6 dimensions

### 12.2 Historical Support Lookup

**Purpose**: Score based on prior approvals of similar patterns

**Pseudocode**:
```
function calculateHistoricalSupport(field_path, old_value, new_value, schema_id):
    // Exact match check
    exact = learning_store.find_exact(schema_id, field_path, old_value, new_value)
    
    if exact:
        recency = calculate_recency_factor(exact.last_applied)
        approval = min(exact.approval_count / 100, 1.0)
        return 0.6 * exact.success_rate + 0.3 * recency + 0.1 * approval
    
    // Fuzzy match check
    fuzzy = learning_store.find_fuzzy(schema_id, field_path, old_value, 0.8)
    
    if fuzzy:
        best = max(fuzzy, key=lambda r: r.similarity)
        return 0.5 * best.similarity + 0.3 * best.success_rate
    
    // Field seen before?
    if learning_store.field_exists(schema_id, field_path):
        return 0.3
    
    return 0.0
```

## 13. Error Handling

| Condition | Code | Status | Recovery |
|-----------|------|--------|----------|
| Schema not found | SCHEMA_NOT_FOUND | 404 | Return error, don't process |
| Record not found | RECORD_NOT_FOUND | 422 | Skip record, continue batch |
| Learning store timeout | HISTORICAL_UNAVAILABLE | 200 | historical_support = 0.0, add flag |
| AI service timeout | SEMANTIC_TIMEOUT | 200 | semantic_coherence = 0.5, add flag |
| Calculation overflow | CALCULATION_ERROR | 500 | Log, alert, fail record |

## 14. Performance Requirements

| Metric | Target | P50 | P95 | P99 |
|--------|--------|-----|-----|-----|
| Single record latency | <100ms | 30ms | 80ms | 150ms |
| Batch (1000 records) | <10s | 5s | 8s | 12s |
| Memory per worker | <512MB | 256MB | 400MB | 500MB |

## 15. Security

**Authentication**: Service-to-service JWT with `confidence:calculate` scope

**Data Sensitivity**:
- Transformed data may contain PII — do not log field values
- Calculation traces stored encrypted at rest

**Audit**: All calculations logged with actor, timestamp, inputs hash

## 16. Testing Strategy

### 16.1 Unit Tests

| Component | Focus | Mocks |
|-----------|-------|-------|
| PatternMatchCalculator | Type coercion, regex matching | None |
| HistoricalSupportCalculator | Exact/fuzzy matching, recency | Learning store |
| SourceProfileCalculator | Hash comparison, similarity | Profile store |
| ValidationPassCalculator | Schema validation, rule evaluation | None |
| WeightedAverageCalculator | Math, adjustments | None |

### 16.2 Integration Tests

| Test | Components | Setup |
|------|------------|-------|
| Full calculation flow | All calculators + service | DB seed, Redis |
| API endpoint | Service + API layer | Mock auth |
| Batch processing | Service + Kafka | Test containers |

### 16.3 Edge Cases

| # | Case | Expected |
|---|------|----------|
| 1 | All dimensions = 1.0 | overall = 1.0 |
| 2 | All dimensions = 0.0 | overall = 0.0 |
| 3 | One dimension = 0.0, others = 1.0 | overall ≤ 0.5, flag applied |
| 4 | Empty transformed_data | validation = 0.0 |
| 5 | 10,000 record batch | Complete <60s, no OOM |
| 6 | Unicode in values | No crash, valid score |
| 7 | Null vs missing keys | Different handling |
| 8 | Historical store down | Graceful degradation |
| 9 | Weights don't sum to 1.0 | Normalization or error |
| 10 | Score at exact boundary 0.85 | Correct level assignment |

## 17. Technical Decisions

### Decision 1: Deterministic vs AI Split

**Context**: Need balance between speed/auditability and intelligence

**Options**:
| Option | Pros | Cons |
|--------|------|------|
| 100% deterministic | Fast, auditable, cheap | Miss semantic issues |
| 100% AI | Smart, catches subtleties | Slow, expensive, non-reproducible |
| Hybrid (80/20) | Best of both | Complexity |

**Decision**: Hybrid — 5 dimensions deterministic, semantic coherence uses AI

**Consequences**: Must handle AI unavailability gracefully; AI dimension optional

### Decision 2: Score Storage

**Context**: Store scores in same DB or separate?

**Decision**: Same PostgreSQL DB, separate table

**Consequences**: Simpler ops, transactional consistency with records

## 18. File Structure

```
src/
├── services/
│   └── confidence/
│       ├── __init__.py
│       ├── service.py              # ConfidenceService orchestrator
│       ├── models.py               # Pydantic models
│       ├── calculator.py           # WeightedAverageCalculator
│       └── dimensions/
│           ├── __init__.py
│           ├── base.py             # DimensionCalculator protocol
│           ├── pattern_match.py
│           ├── historical_support.py
│           ├── source_profile.py
│           ├── validation_pass.py
│           ├── semantic_coherence.py
│           └── business_rules.py
├── api/
│   └── v1/
│       └── confidence.py           # FastAPI endpoints
└── tests/
    └── services/
        └── confidence/
            ├── conftest.py         # Fixtures
            ├── test_service.py
            ├── test_calculator.py
            └── test_dimensions/
                ├── test_pattern_match.py
                └── ...
```

---

## TIER 3: TASKS (Build Steps)

---

## 19. Task Sequence

### Pre-Implementation Checklist

- [ ] Database migration for confidence_scores table
- [ ] Pydantic model conventions established
- [ ] Test fixtures for schemas and records
- [ ] Redis test container in CI

### Task Overview

| Task | Description | Test File | Size |
|------|-------------|-----------|------|
| T1 | Pydantic models | test_models.py | S |
| T2 | Pattern match dimension | test_pattern_match.py | M |
| T3 | Validation pass dimension | test_validation_pass.py | M |
| T4 | Historical support dimension | test_historical_support.py | M |
| T5 | Source profile dimension | test_source_profile.py | M |
| T6 | Business rules dimension | test_business_rules.py | S |
| T7 | Weighted average calculator | test_calculator.py | S |
| T8 | Confidence service | test_service.py | M |
| T9 | API endpoints | test_api.py | M |
| T10 | Integration tests | test_integration.py | L |

---

### TASK T1: Pydantic Models

**Objective**: Create all data models for confidence scoring

**Prerequisites**: Pydantic v2 installed

**Files to Create**:
- `src/services/confidence/models.py`

**Implementation Steps**:
1. Create `DimensionScore` with score, weight, details
2. Create `ConfidenceResult` with all fields
3. Create request/response models
4. Add validators for score range, weight sum

**Test Specification**:
```python
# tests/services/confidence/test_models.py

import pytest
from pydantic import ValidationError
from src.services.confidence.models import DimensionScore, ConfidenceResult

class TestDimensionScore:
    def test_valid_score_accepted(self):
        """Given valid score 0.85, When created, Then succeeds"""
        score = DimensionScore(score=0.85, weight=0.25, details="test")
        assert score.score == 0.85

    def test_score_below_zero_rejected(self):
        """Given score -0.1, When created, Then ValidationError"""
        with pytest.raises(ValidationError):
            DimensionScore(score=-0.1, weight=0.25, details="test")

    def test_score_above_one_rejected(self):
        """Given score 1.1, When created, Then ValidationError"""
        with pytest.raises(ValidationError):
            DimensionScore(score=1.1, weight=0.25, details="test")
```

**Verification**: `pytest tests/services/confidence/test_models.py -v`

**Definition of Done**:
- [ ] All tests pass
- [ ] Type hints complete
- [ ] Docstrings on models

---

### TASK T2: Pattern Match Dimension

**Objective**: Calculate how well values match expected field patterns

**Prerequisites**: T1 complete

**Files to Create**:
- `src/services/confidence/dimensions/pattern_match.py`
- `src/services/confidence/dimensions/__init__.py`

**Test Specification**:
```python
# tests/services/confidence/test_dimensions/test_pattern_match.py

class TestPatternMatchCalculator:
    def test_exact_type_and_pattern_scores_one(self):
        """Given ISO date matching pattern, Then score = 1.0"""
        calc = PatternMatchCalculator()
        field = {"type": "string", "pattern": r"^\d{4}-\d{2}-\d{2}$"}
        result = calc.calculate("2024-01-15", field)
        assert result.score == 1.0

    def test_coercible_type_reduces_score(self):
        """Given int for string field, Then score ~0.8"""
        calc = PatternMatchCalculator()
        field = {"type": "string"}
        result = calc.calculate(42, field)
        assert 0.7 <= result.score <= 0.9

    def test_incompatible_type_scores_zero(self):
        """Given object for integer field, Then score = 0.0"""
        calc = PatternMatchCalculator()
        field = {"type": "integer"}
        result = calc.calculate({"nested": "obj"}, field)
        assert result.score == 0.0
```

**Verification**: `pytest tests/services/confidence/test_dimensions/test_pattern_match.py -v`

---

### TASK T7: Weighted Average Calculator

**Objective**: Combine dimension scores into overall confidence

**Prerequisites**: T1 complete

**Files to Create**:
- `src/services/confidence/calculator.py`

**Test Specification**:
```python
# tests/services/confidence/test_calculator.py

class TestWeightedAverageCalculator:
    def test_calculates_correct_weighted_average(self):
        """Given known dimension scores, Then weighted average correct"""
        dimensions = [
            DimensionScore(score=0.9, weight=0.25, details=""),
            DimensionScore(score=0.8, weight=0.25, details=""),
            DimensionScore(score=1.0, weight=0.15, details=""),
            DimensionScore(score=1.0, weight=0.20, details=""),
            DimensionScore(score=0.7, weight=0.10, details=""),
            DimensionScore(score=1.0, weight=0.05, details=""),
        ]
        result = WeightedAverageCalculator().calculate(dimensions)
        assert result.overall_score == pytest.approx(0.895, rel=0.01)

    def test_zero_dimension_caps_at_half(self):
        """Given one dimension = 0.0, Then overall ≤ 0.5"""
        dimensions = [
            DimensionScore(score=1.0, weight=0.25, details=""),
            DimensionScore(score=0.0, weight=0.25, details=""),  # Zero!
            DimensionScore(score=1.0, weight=0.50, details=""),
        ]
        result = WeightedAverageCalculator().calculate(dimensions)
        assert result.overall_score <= 0.5
        assert "zero_dimension_cap" in result.applied_adjustments
```

**Verification**: `pytest tests/services/confidence/test_calculator.py -v`

---

### TASK T8: Confidence Service

**Objective**: Orchestrate all dimensions into complete confidence calculation

**Prerequisites**: T1-T7 complete

**Files to Create**:
- `src/services/confidence/service.py`

**Test Specification**:
```python
# tests/services/confidence/test_service.py

class TestConfidenceService:
    @pytest.fixture
    def service(self, mock_calculators):
        return ConfidenceService(**mock_calculators)

    def test_includes_all_dimensions(self, service):
        """Given valid input, Then result has all 6 dimensions"""
        result = service.calculate(record={}, schema={}, metadata={})
        assert len(result.dimensions) == 6
        assert "pattern_match" in result.dimensions
        assert "historical_support" in result.dimensions

    def test_determines_correct_review_decision(self, service):
        """Given high confidence, Then AUTO_APPROVE"""
        # Configure mocks to return high scores
        result = service.calculate(record={}, schema={}, metadata={})
        assert result.review_decision == "AUTO_APPROVE"
```

**Verification**: `pytest tests/services/confidence/test_service.py -v`

---

[Tasks T3-T6, T9-T10 follow same pattern]
