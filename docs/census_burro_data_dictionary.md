# Census Burro: Data Dictionary & Encoding Methodology

## Purpose & Scope

This document defines the data structures, encoding methodologies, and annotation procedures for the Census Burro evaluation system. It establishes the standardized format for questions, responses, and quality assessments that will form the "ground truth" dataset for system evaluation and improvement.

---

## Database Schema Overview

### Core Tables

```sql
-- Primary question repository
questions (
    question_id,
    question_text,
    category,
    complexity_level,
    risk_level,
    source_document,
    created_date,
    status
)

-- Gold standard responses
gold_responses (
    response_id,
    question_id,
    response_text,
    source_citations,
    escalation_required,
    safety_critical,
    approved_by,
    approval_date
)

-- System-generated responses for evaluation
system_responses (
    system_response_id,
    question_id,
    response_text,
    model_version,
    timestamp,
    confidence_score
)

-- Human evaluations
evaluations (
    evaluation_id,
    system_response_id,
    evaluator_id,
    source_fidelity_score,
    practical_utility_score,
    safety_compliance_score,
    user_experience_score,
    total_score,
    comments,
    evaluation_date
)
```

---

## Question Encoding Schema

### **questions** Table Structure

| Field | Type | Description | Values/Format | Required |
|-------|------|-------------|---------------|----------|
| `question_id` | VARCHAR(20) | Unique identifier | CB-YYYY-NNNN format | Yes |
| `question_text` | TEXT | Actual question content | Natural language | Yes |
| `category` | ENUM | Content category | SAFETY, PROCEDURAL, TECHNICAL, ADMIN, LEGAL | Yes |
| `complexity_level` | INT | Difficulty level | 1-4 (Basic to Expert) | Yes |
| `risk_level` | ENUM | Risk assessment | CRITICAL, HIGH, MEDIUM, LOW | Yes |
| `source_document` | VARCHAR(255) | Originating document | Document name/version | No |
| `scenario_type` | VARCHAR(100) | Specific scenario | Free text descriptor | No |
| `keywords` | JSON | Searchable terms | Array of strings | No |
| `created_date` | DATETIME | Creation timestamp | ISO 8601 format | Yes |
| `created_by` | VARCHAR(50) | Creator identifier | SME ID or system | Yes |
| `status` | ENUM | Current status | DRAFT, ACTIVE, ARCHIVED, DEPRECATED | Yes |
| `review_frequency` | ENUM | How often to test | DAILY, WEEKLY, MONTHLY, QUARTERLY | No |

### **Category Encoding**

| Code | Description | Examples |
|------|-------------|----------|
| SAFETY | Safety & Security situations | Hostile residents, dangerous areas, threats |
| PROCEDURAL | Enumeration procedures | Form completion, callbacks, documentation |
| TECHNICAL | Equipment & technical issues | Device troubleshooting, connectivity |
| ADMIN | Administrative tasks | Timesheets, scheduling, contacts |
| LEGAL | Legal & compliance matters | Privacy, refusals, escalation protocols |

### **Complexity Level Encoding**

| Level | Code | Description | Characteristics |
|-------|------|-------------|-----------------|
| 1 | BASIC | Direct lookup | Single fact, clear procedure |
| 2 | INTERMEDIATE | Multi-step process | Sequential actions, decision points |
| 3 | ADVANCED | Judgment required | Context-dependent, experience needed |
| 4 | EXPERT | Supervisor escalation | Complex, safety-critical, ambiguous |

### **Risk Level Encoding**

| Level | Impact | Response Requirements |
|-------|--------|----------------------|
| CRITICAL | Immediate safety/legal risk | Mandatory escalation, safety-first messaging |
| HIGH | Data quality/employment impact | Clear procedures, escalation triggers |
| MEDIUM | Operational efficiency | Standard procedures, optional escalation |
| LOW | Minor procedural questions | Direct answers, self-service |

---

## Response Encoding Schema

### **gold_responses** Table Structure

| Field | Type | Description | Values/Format | Required |
|-------|------|-------------|---------------|----------|
| `response_id` | VARCHAR(20) | Unique identifier | GR-YYYY-NNNN format | Yes |
| `question_id` | VARCHAR(20) | Foreign key to questions | CB-YYYY-NNNN format | Yes |
| `response_text` | TEXT | Gold standard answer | Structured response format | Yes |
| `source_citations` | JSON | Document references | Array of citation objects | Yes |
| `escalation_required` | BOOLEAN | Requires supervisor contact | TRUE/FALSE | Yes |
| `safety_critical` | BOOLEAN | Safety implications | TRUE/FALSE | Yes |
| `response_type` | ENUM | Type of guidance | FACTUAL, PROCEDURAL, ESCALATION, SAFETY | Yes |
| `confidence_level` | ENUM | Certainty of answer | HIGH, MEDIUM, LOW | Yes |
| `approved_by` | VARCHAR(50) | SME who approved | SME identifier | Yes |
| `approval_date` | DATETIME | Approval timestamp | ISO 8601 format | Yes |
| `version` | INT | Version number | Incremental integer | Yes |
| `tags` | JSON | Additional metadata | Array of strings | No |

### **Response Structure Format**

```json
{
  "response_text": "Structured answer following template",
  "source_citations": [
    {
      "document": "Field Safety Manual",
      "section": "3.2.1",
      "page": 45,
      "excerpt": "Relevant quote from source"
    }
  ],
  "escalation_required": true,
  "safety_critical": true,
  "response_type": "SAFETY",
  "confidence_level": "HIGH"
}
```

### **Citation Object Schema**

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `document` | STRING | Source document name | "Field Representative Handbook" |
| `section` | STRING | Section/chapter reference | "Chapter 4, Section 2.1" |
| `page` | INT | Page number (if applicable) | 127 |
| `excerpt` | STRING | Relevant quote/snippet | "Safety is the top priority..." |
| `url` | STRING | Document URL (if available) | "https://..." |
| `date_accessed` | DATE | When citation was verified | "2024-01-15" |

---

## Evaluation Encoding Schema

### **evaluations** Table Structure

| Field | Type | Description | Values/Format | Required |
|-------|------|-------------|---------------|----------|
| `evaluation_id` | VARCHAR(20) | Unique identifier | EV-YYYY-NNNN format | Yes |
| `system_response_id` | VARCHAR(20) | Response being evaluated | SR-YYYY-NNNN format | Yes |
| `evaluator_id` | VARCHAR(50) | Human or system evaluator | SME ID or system identifier | Yes |
| `evaluator_type` | ENUM | Type of evaluator | HUMAN, GPT4, CLAUDE, ENSEMBLE | Yes |
| `source_fidelity_score` | DECIMAL(4,2) | Accuracy score (0-40) | 0.00 to 40.00 | Yes |
| `practical_utility_score` | DECIMAL(4,2) | Utility score (0-30) | 0.00 to 30.00 | Yes |
| `safety_compliance_score` | DECIMAL(4,2) | Safety score (0-20) | 0.00 to 20.00 | Yes |
| `user_experience_score` | DECIMAL(4,2) | UX score (0-10) | 0.00 to 10.00 | Yes |
| `total_score` | DECIMAL(5,2) | Combined score (0-100) | 0.00 to 100.00 | Yes |
| `pass_threshold` | DECIMAL(5,2) | Minimum acceptable score | Based on risk level | Yes |
| `evaluation_result` | ENUM | Pass/fail determination | PASS, FAIL, REVIEW_REQUIRED | Yes |
| `comments` | TEXT | Detailed feedback | Free text | No |
| `evaluation_date` | DATETIME | When evaluation occurred | ISO 8601 format | Yes |
| `review_duration` | INT | Time spent reviewing (minutes) | Integer | No |

### **Score Breakdown Schema**

```json
{
  "source_fidelity": {
    "accuracy": 18.5,
    "currency": 9.0,
    "completeness": 8.5,
    "total": 36.0,
    "max_possible": 40.0
  },
  "practical_utility": {
    "actionability": 14.0,
    "context_appropriateness": 8.5,
    "next_steps": 4.5,
    "total": 27.0,
    "max_possible": 30.0
  },
  "safety_compliance": {
    "safety_protocol_adherence": 9.5,
    "legal_compliance": 4.5,
    "escalation_triggers": 5.0,
    "total": 19.0,
    "max_possible": 20.0
  },
  "user_experience": {
    "clarity": 4.5,
    "tone": 2.5,
    "formatting": 2.0,
    "total": 9.0,
    "max_possible": 10.0
  }
}
```

---

## SME Annotation Procedures

### **Annotation Workflow**

1. **Question Assignment**
   - SMEs assigned questions based on expertise area
   - Each question reviewed by minimum 2 SMEs
   - Conflicts resolved by senior SME or committee

2. **Scoring Process**
   - Independent scoring using standardized rubrics
   - Detailed comments required for scores <70
   - Citation verification for all source references

3. **Consensus Building**
   - Compare scores between evaluators
   - Discuss discrepancies >10 points
   - Document rationale for final scores

### **Quality Control Checks**

```python
# Example validation rules
def validate_evaluation(evaluation):
    checks = []
    
    # Score consistency
    if abs(evaluation.total_score - sum(component_scores)) > 0.01:
        checks.append("SCORE_MISMATCH")
    
    # Critical safety violations
    if evaluation.safety_compliance_score < 10 and evaluation.question.risk_level == "CRITICAL":
        checks.append("CRITICAL_SAFETY_FAILURE")
    
    # Missing required fields
    if not evaluation.comments and evaluation.total_score < 70:
        checks.append("MISSING_COMMENTS")
    
    return checks
```

### **Inter-rater Reliability Metrics**

| Metric | Calculation | Target | Action if Below Target |
|--------|-------------|--------|------------------------|
| **Agreement Rate** | Scores within 10 points | >85% | Additional calibration training |
| **Correlation** | Pearson correlation coefficient | >0.80 | Reviewer re-training |
| **Bias Detection** | Systematic scoring differences | <5 points | Individual feedback |

---

## Machine Scoring Integration

### **Automated Evaluation Pipeline**

```python
# Example automated scoring workflow
class AutomatedEvaluator:
    def __init__(self, primary_model, secondary_model):
        self.primary = primary_model  # e.g., GPT-4
        self.secondary = secondary_model  # e.g., Claude
    
    def evaluate_response(self, question, system_response, gold_standard):
        # Primary evaluation
        primary_scores = self.primary.score(question, system_response, gold_standard)
        
        # Secondary evaluation
        secondary_scores = self.secondary.score(question, system_response, gold_standard)
        
        # Consensus logic
        if abs(primary_scores.total - secondary_scores.total) < 10:
            return self.average_scores(primary_scores, secondary_scores)
        else:
            return self.flag_for_human_review(question, system_response, primary_scores, secondary_scores)
```

### **Ensemble Scoring Schema**

| Field | Type | Description |
|-------|------|-------------|
| `ensemble_id` | VARCHAR(20) | Unique ensemble evaluation ID |
| `primary_model_score` | DECIMAL(5,2) | Primary model evaluation |
| `secondary_model_score` | DECIMAL(5,2) | Secondary model evaluation |
| `consensus_score` | DECIMAL(5,2) | Final consensus score |
| `confidence_interval` | DECIMAL(5,2) | Uncertainty measure |
| `human_review_required` | BOOLEAN | Flag for human oversight |

---

## Data Validation & Quality Assurance

### **Required Field Validation**

```sql
-- Constraints to ensure data quality
ALTER TABLE questions 
ADD CONSTRAINT chk_complexity_level 
CHECK (complexity_level BETWEEN 1 AND 4);

ALTER TABLE evaluations 
ADD CONSTRAINT chk_total_score 
CHECK (total_score BETWEEN 0 AND 100);

ALTER TABLE gold_responses 
ADD CONSTRAINT chk_source_citations 
CHECK (JSON_VALID(source_citations));
```

### **Data Integrity Checks**

| Check Type | Frequency | Action |
|------------|-----------|--------|
| **Orphaned Records** | Daily | Alert and cleanup |
| **Score Consistency** | On insert/update | Reject invalid data |
| **Citation Validation** | Weekly | Flag broken links |
| **Duplicate Detection** | Daily | Merge or flag duplicates |

---

## Export & Import Formats

### **Standard Export Format (CSV)**

```csv
question_id,category,complexity,risk_level,question_text,gold_response,total_score,evaluation_date
CB-2024-0001,SAFETY,3,CRITICAL,"Threatening signs posted","{response_text}",95.5,2024-01-15T10:30:00Z
```

### **JSON Export Format**

```json
{
  "question": {
    "id": "CB-2024-0001",
    "text": "There are threatening signs posted",
    "category": "SAFETY",
    "complexity": 3,
    "risk_level": "CRITICAL"
  },
  "gold_response": {
    "text": "Your safety is the top priority...",
    "citations": [...],
    "escalation_required": true
  },
  "evaluations": [
    {
      "evaluator": "SME-001",
      "scores": {...},
      "total": 95.5
    }
  ]
}
```

---

## Implementation Guidelines

### **Phase 1: Manual Data Entry**
- Use standardized spreadsheet templates
- Validate data before database import
- Implement double-entry verification

### **Phase 2: Web Interface**
- Build evaluation interface for SMEs
- Real-time validation and scoring
- Automated conflict detection

### **Phase 3: API Integration**
- RESTful API for system integration
- Automated evaluation pipeline
- Real-time monitoring dashboards

This data dictionary provides the foundation for consistent, scalable evaluation of the Census Burro system while maintaining data integrity and enabling both human and automated quality assurance processes.