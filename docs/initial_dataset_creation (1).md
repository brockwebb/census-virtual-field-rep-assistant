# Census Burro: Initial Dataset Creation Strategy

## Executive Summary

This document outlines the AI-assisted approach for rapidly creating the initial evaluation dataset for Census Burro. Rather than requiring SMEs to create hundreds of questions and answers from scratch, we leverage GPT to generate comprehensive Q&A pairs with initial scoring, then have SMEs review and refine the output. This approach reduces initial creation time from months to weeks while maintaining quality through human oversight.

---

## Bootstrap Strategy Overview

### **The Problem with Traditional Approaches**
- SMEs hate creating content from blank pages
- Manual creation of 200+ Q&A pairs takes months
- Inconsistent formatting and coverage gaps
- Initial scoring calibration is time-intensive
- High abandonment rate for large-scale content creation projects

### **AI-Assisted Solution**
```
Census Documents → GPT Analysis → Generated Q&A Pairs → 
Self-Scored Responses → SME Review & Adjustment → Production Dataset
```

### **Key Benefits**
- **Speed:** Generate 200+ Q&A pairs in hours, not months
- **Consistency:** Standardized format and comprehensive coverage
- **SME Efficiency:** Focus on refinement rather than creation
- **Quality Assurance:** Human oversight where it matters most
- **Scalability:** Easy to expand dataset as new scenarios emerge

---

## Implementation Methodology

### **Phase 1: Document Ingestion & Analysis**

#### **Document Preparation**
1. **Primary Sources:**
   - Field Representative Handbook
   - Safety and Security Protocols
   - Enumeration Procedures Manual
   - Equipment Troubleshooting Guides
   - Administrative Procedures

2. **Document Processing:**
   - Convert PDFs to searchable text
   - Extract key procedures and protocols
   - Identify frequently referenced sections
   - Create document index for citation purposes

### **Phase 2: Automated Question Generation**

#### **GPT Configuration for Question Creation**

```
SYSTEM PROMPT:
You are a Census Bureau evaluation specialist creating test questions for the Census Burro virtual assistant. Your goal is to create realistic field scenarios that Field Representatives encounter, covering all operational categories and complexity levels.

Generate questions that:
1. Reflect real field situations FRs face
2. Cover all risk levels (Critical, High, Medium, Low)
3. Span all complexity levels (Basic, Intermediate, Advanced, Expert)
4. Include specific details that make scenarios realistic
5. Test both common and edge-case situations

Use these categories:
- SAFETY: Hostile residents, dangerous areas, threatening signage, personal security
- PROCEDURAL: Enumeration techniques, form completion, callbacks, documentation
- TECHNICAL: Equipment issues, connectivity problems, device troubleshooting
- ADMIN: Timesheets, mileage, scheduling, supervisor contact
- LEGAL: Privacy laws, refusal handling, minor enumeration, confidentiality

For each question, specify:
- Category (SAFETY/PROCEDURAL/TECHNICAL/ADMIN/LEGAL)
- Complexity Level (1-4)
- Risk Level (CRITICAL/HIGH/MEDIUM/LOW)
- Realistic scenario context
```

#### **Question Generation Prompts**

**Safety Scenarios (Critical/High Risk):**
```
Generate exactly 3 safety-related questions with complete responses and scoring. Focus on:
- Threatening signage or verbal threats
- Unsafe neighborhood conditions  
- Aggressive residents or animals

Make each scenario specific and realistic. Include details about what the FR sees/hears.

For each question, provide:
1. Realistic scenario question
2. Complete gold standard response with source citations
3. Self-evaluation using our 100-point rubric
4. Categorization (SAFETY, complexity level, risk level)

Example format:
"I'm at 123 Oak Street and there's a sign that says 'Armed and Dangerous - No Trespassing.' The property looks occupied but I can see someone watching me from the window. What should I do?"

Repeat this process multiple times to generate the full set of 20 safety scenarios.
```

**Procedural Scenarios (Medium Risk):**
```
Generate exactly 3 procedural questions with complete responses and scoring. Focus on:
- Gated community access procedures
- Partial interview completion
- Callback scheduling protocols

For each question, provide complete response and self-evaluation.
Focus on step-by-step processes and decision points.

Repeat this process multiple times to generate the full set of 30 procedural scenarios.
```

**Technical Scenarios (Low/Medium Risk):**
```
Generate exactly 3 technical support questions with complete responses and scoring. Focus on:
- Tablet/device connectivity issues
- Software problems or crashes
- Data synchronization failures

For each question, provide complete response and self-evaluation.

Repeat this process multiple times to generate the full set of 25 technical scenarios.
```

**Administrative Scenarios (Low Risk):**
```
Generate exactly 3 administrative questions with complete responses and scoring. Focus on:
- Timesheet submission procedures
- Mileage recording and reimbursement
- Supervisor contact protocols

For each question, provide complete response and self-evaluation.

Repeat this process multiple times to generate the full set of 15 administrative scenarios.
```

**Legal/Compliance Scenarios (High Risk):**
```
Generate exactly 3 legal and compliance questions with complete responses and scoring. Focus on:
- Minor enumeration without parent present
- Privacy and confidentiality requirements
- Refusal handling procedures

For each question, provide complete response and self-evaluation.

Repeat this process multiple times to generate the full set of 20 legal scenarios.
```

### **Phase 3: Automated Response Generation**

#### **Gold Standard Response Creation**

```
SYSTEM PROMPT:
You are Count Burro, the Census Bureau's virtual assistant. Create gold standard responses to Field Representative questions using the following structure:

RESPONSE FORMAT:
1. Immediate Action (if applicable)
2. Step-by-Step Procedure
3. Documentation Requirements
4. When to Escalate
5. Source Citation

TONE: Professional but approachable, safety-first, clear and actionable.

For safety issues: Always prioritize FR safety over data collection
For procedures: Provide clear, sequential steps
For technical issues: Include troubleshooting sequence
For admin: Direct to specific forms/contacts
For legal: Emphasize compliance and escalation when uncertain

Include proper source citations in this format:
Source: [Document Name], Section [X.X], Page [XX]

Example response structure:
"Your safety is the top priority. Here's what to do:
1. [Immediate action]
2. [Next steps]
3. [Documentation needed]
4. [When to contact supervisor]

Source: Field Safety Manual, Section 3.2, Page 45"
```

### **Phase 4: Automated Self-Evaluation**

#### **Self-Scoring Methodology**

```
EVALUATION PROMPT:
Evaluate your response using the Census Burro scoring rubric (100 points total):

SOURCE FIDELITY (40 points):
- Accuracy (20 pts): Information matches official documentation
- Currency (10 pts): Information is current and not outdated  
- Completeness (10 pts): All relevant aspects are addressed

PRACTICAL UTILITY (30 points):
- Actionability (15 pts): Clear, implementable guidance
- Context Appropriateness (10 pts): Fits the specific situation
- Next Steps (5 pts): Clear path forward provided

SAFETY & COMPLIANCE (20 points):
- Safety Protocol Adherence (10 pts): Follows established safety procedures
- Legal Compliance (5 pts): Meets privacy and legal requirements
- Escalation Triggers (5 pts): Appropriate escalation recommendations

USER EXPERIENCE (10 points):
- Clarity (5 pts): Easy to understand and follow
- Tone (3 pts): Professional yet approachable
- Formatting (2 pts): Well-structured, scannable

For each component, provide:
1. Score (specific points)
2. Brief justification
3. Total score
4. Pass/Fail determination (>70 = Pass)

Output in JSON format matching our database schema.
```

### **Phase 5: SME Review Interface**

#### **Review Workflow Design**

**SME Dashboard Structure:**
```
Question Review Queue
├── High Priority (Critical/High Risk) - Review First
├── Medium Priority (Medium Risk) - Sample Review
└── Low Priority (Low Risk) - Spot Check

For each question:
├── Generated Question & Metadata
├── AI Response & Self-Score
├── Source Citations
├── Adjustment Interface
├── Approval/Rejection Options
└── Comments Section
```

#### **SME Review Template**

```
Question ID: CB-2024-0001
Category: SAFETY | Complexity: 3 | Risk: CRITICAL

SCENARIO:
"I'm at 123 Oak Street and there's a sign that says 'Armed and Dangerous - No Trespassing.' 
The property looks occupied but I can see someone watching me from the window. What should I do?"

AI GENERATED RESPONSE:
"Your safety is the absolute priority. Do not approach this property. Here's what to do:
1. Leave the immediate area and return to your vehicle
2. Document the address, signage observed, and safety concerns in your field notes
3. Contact your supervisor immediately using the emergency contact protocol
4. Use case code 'Dangerous Location - Safety Concern' in your case management system

Source: Field Safety Manual, Section 3.2.1, Page 47"

AI SELF-SCORE: 92/100
├── Source Fidelity: 38/40 (Accurate citation, current info, comprehensive)
├── Practical Utility: 28/30 (Immediately actionable, contextually appropriate)
├── Safety Compliance: 20/20 (Perfect safety compliance, appropriate escalation)
└── User Experience: 6/10 (Clear but could improve formatting)

SME REVIEW:
☐ Response is accurate and appropriate
☐ Minor adjustments needed
☐ Major revision required
☐ Reject and regenerate

Score Adjustment: [___]/100 (Optional - explain if changed)

Comments: ________________________________

Source Verification:
☐ Citation is accurate
☐ Citation needs correction: _______________

Approve for Gold Standard: [YES] [NO] [NEEDS REVISION]
```

---

## Quality Assurance Procedures

### **Automated Validation Checks**

```python
def validate_generated_content(question, response, score):
    issues = []
    
    # Required field validation
    if not question.get('category') in VALID_CATEGORIES:
        issues.append("Invalid category")
    
    if not question.get('complexity_level') in [1,2,3,4]:
        issues.append("Invalid complexity level")
    
    # Response quality checks
    if len(response.get('response_text', '')) < 50:
        issues.append("Response too short")
    
    if not response.get('source_citations'):
        issues.append("Missing source citations")
    
    # Score validation
    if score.get('total_score') != sum(score.get('component_scores', [])):
        issues.append("Score calculation error")
    
    # Safety checks for high-risk scenarios
    if question.get('risk_level') == 'CRITICAL':
        safety_keywords = ['safety', 'supervisor', 'escalate', 'contact']
        if not any(keyword in response.get('response_text', '').lower() 
                  for keyword in safety_keywords):
            issues.append("Critical scenario missing safety language")
    
    return issues
```

### **Coverage Analysis**

**Ensure comprehensive scenario coverage:**
```
Category Distribution Target:
├── SAFETY: 30% (60 questions) - Heavy focus on high-risk scenarios
├── PROCEDURAL: 35% (70 questions) - Most common FR questions
├── TECHNICAL: 15% (30 questions) - Equipment and connectivity
├── ADMIN: 10% (20 questions) - Basic operational needs
└── LEGAL: 10% (20 questions) - Compliance and escalation

Complexity Distribution:
├── Level 1 (Basic): 30%
├── Level 2 (Intermediate): 40%
├── Level 3 (Advanced): 25%
└── Level 4 (Expert): 5%

Risk Distribution:
├── CRITICAL: 15% (30 questions)
├── HIGH: 25% (50 questions)
├── MEDIUM: 40% (80 questions)
└── LOW: 20% (40 questions)
```

---

## Implementation Timeline

### **Week 1: Content Generation**
- **Day 1-2:** Document processing and GPT configuration
- **Day 3-4:** Generate content in small batches (3 questions at a time with full responses and scoring)
- **Day 5:** Automated validation and initial quality checks

### **Week 2: SME Review (Critical/High Risk)**
- **Day 1-3:** SME review of 80 Critical/High risk questions (generated in ~27 batches of 3)
- **Day 4-5:** Consensus building and conflict resolution

### **Week 3: SME Validation (Sample Review)**
- **Day 1-3:** Random sample review of Medium/Low risk questions (40 questions from ~13 batches)
- **Day 4-5:** Calibration analysis and scoring adjustment

### **Week 4: Dataset Finalization**
- **Day 1-2:** Incorporate SME feedback and corrections
- **Day 3-4:** Final validation and database population
- **Day 5:** Documentation and handoff to evaluation team

**Note:** Total of ~67 generation cycles (3 questions each) to reach 200 questions, ensuring consistent quality throughout.

---

## Success Metrics

### **Quantity Targets**
- **200+ total questions** across all categories
- **100% coverage** of identified scenario types
- **Balanced distribution** across complexity and risk levels

### **Quality Targets**
- **>85% SME approval rate** for AI-generated content
- **<10 point average** adjustment in SME scoring
- **>90% citation accuracy** in source references
- **Zero critical safety errors** in high-risk scenarios

### **Efficiency Metrics**
- **<20 hours total SME time** for initial dataset creation
- **<5 days** from start to production-ready dataset
- **>80% time savings** vs. manual creation approach

---

## Risk Mitigation

### **Content Quality Risks**
- **Mitigation:** Multi-layer validation (automated + SME review)
- **Fallback:** Flag questionable content for manual creation

### **SME Availability Risks**
- **Mitigation:** Stagger review periods, prioritize critical content
- **Fallback:** Use senior SME as tie-breaker for conflicts

### **Technical Generation Risks**
- **Mitigation:** Validate AI outputs against source documents
- **Fallback:** Manual creation for categories with poor AI performance

### **Coverage Gap Risks**
- **Mitigation:** Systematic scenario mapping before generation
- **Fallback:** Iterative expansion based on real user questions

---

## Future Enhancements

### **Continuous Dataset Expansion**
- **Real user questions** → Auto-categorization → SME review → Dataset addition
- **Seasonal scenarios** (decennial operations, special surveys)
- **Regional variations** in procedures and challenges

### **Automated Quality Monitoring**
- **Performance tracking** of generated vs. manually created content
- **SME feedback patterns** to improve generation prompts
- **User satisfaction correlation** with content source

This approach transforms the traditionally labor-intensive process of evaluation dataset creation into an efficient, AI-assisted workflow that leverages the strengths of both artificial intelligence and human expertise.
