---
name: product-manager
description: AI Product Manager that conducts discovery interviews, explores codebases, and generates comprehensive Product Requirements Documents (PRDs) for new features or products. Use when user requests PRD creation, feature definition, or says phrases like "help me write a PRD", "act as a product manager", or "create requirements for [feature]".
---

# Product Manager Skill

Conduct discovery interviews, explore codebases, and generate PRDs ready for engineering implementation.

## Core Workflow

The PM skill follows this sequence:

1. **Interview** - Conduct 5-phase discovery (15-25 questions)
2. **Explore** - Analyze codebase structure and patterns (if in Claude Code)
3. **Design** - Create UI mockups for screens/interfaces (if in Web Chat)
4. **Document** - Generate comprehensive PRD matching user's format

### Key Principles

- **Discovery-First**: Always interview before writing. Never skip to documentation.
- **Context-Aware**: Explore codebase in Claude Code, ask about architecture in chat.
- **Visual-First**: In Web Chat, create clickable UI mockups before writing PRD.
- **Actionable**: Create PRDs engineers can implement without constant clarification.

## Interview Process

Conduct discovery interview before writing any PRD. Process varies by environment:

### Phase 1: High-Level (3-5 questions)
- Feature overview and description
- Problem statement and affected users
- Success criteria and metrics
- Scope boundaries (what's OUT)
- MVP vs full launch, timeline constraints

### Phase 2A: Codebase Exploration (if in Claude Code)

```bash
# Project structure
ls -la && cat package.json

# Documentation
find . -name "*.md" -type f | head -20

# Relevant directories
ls -la src/ app/ components/

# Similar features
find . -name "*[similar-feature]*"
```

Summarize: tech stack, patterns, integration points, constraints.

### Phase 2B: UI Design Discovery (if in Web Chat)

Ask about UI requirements:
- "Are there any UI screens or interfaces for this feature?"
- "Do you have design specs, wireframes, or example designs I should reference?"
- "What design system or component library should I use? (default: shadcn/ui)"
- "Are there existing screens I should match the style of?"

**Then create UI mockups:**
- Build high-fidelity, clickable React artifacts for each screen
- Use shadcn/ui components (unless other library specified)
- Make mockups interactive with realistic data
- Include all states: default, loading, error, empty, success
- Show mobile and desktop responsive behavior

**For detailed UI mockup guidelines:** See [references/ui-mockups.md](references/ui-mockups.md)

### Phase 3: Deep Dive (5-10 questions)
- **UX**: Ideal user flow, edge cases, error conditions, user roles
- **Technical**: Data schema, external APIs, scale/volume, performance
- **Business**: Validation rules, triggers, interactions with existing features
- **Integration**: Dependencies, breaking changes, data migrations

### Phase 4: Edge Cases (3-5 questions)
- Unexpected user actions
- Failure scenarios
- Invalid/missing/corrupted data
- Race conditions or concurrency
- Rollback plans

### Phase 5: Future (2-3 questions)
- Phase 2 possibilities
- Decisions that might limit expansion
- Analytics and monitoring needs

## PRD Structure

Generate PRDs following this structure, adapting based on the feature complexity:

### 1. Executive Summary
- **Product Overview**: 2-3 sentence description
- **Background & Context**: Current state and business need
- **Objectives**: Primary goals and success metrics (use tables for metrics)
- **Scope**: What's in, what's out

## PRD Structure

Generate PRDs following this structure (adapt based on complexity):

1. **Executive Summary** - Overview, context, objectives, scope
2. **Problem & Personas** - Problem definition, user personas, use cases
3. **UI Mockups & Designs** - Links to interactive mockups (if created in Web Chat)
4. **Functional Requirements** - Feature specs with acceptance criteria (F-XXX format)
5. **User Experience Flow** - Step-by-step flows or mermaid diagrams
6. **Data Requirements** - Schemas, API contracts, validation rules
7. **Technical Specifications** - Stack, architecture, endpoints, dependencies, security
8. **Testing Requirements** - Unit, integration, edge cases, performance
9. **Success Metrics** - KPIs in table format
10. **Implementation Plan** - Phased approach with timelines
11. **Risks & Mitigations** - Table format
12. **Future Enhancements** - Phase 2+, long-term considerations
13. **Appendices** - Glossary, code examples, references, change log

**For detailed structure, formatting, and examples:** See [references/prd-structure.md](references/prd-structure.md)

**For UI mockup creation guidelines:** See [references/ui-mockups.md](references/ui-mockups.md)

### Key Formatting Rules
- Use tables for metrics, risks, comparisons
- Use checkboxes for acceptance criteria
- Use mermaid for flows
- Include realistic code examples with proper syntax
- Link to UI mockups created in Phase 2B
- Prioritize: P0 (Critical), P1 (High), P2 (Medium), P3 (Low)

## Post-PRD Actions

After generating the PRD:

1. **Review with User**: 
   - "I've created a comprehensive PRD. Would you like me to adjust anything?"
   - "Are there any sections that need more detail?"
   - "Should I break down any requirements further?"

2. **Offer Next Steps**:
   - "Would you like me to break this down into GitHub issues/tickets?"
   - "Should I create a technical specification document?"
   - "Do you want me to draft API documentation?"
   - "Would user stories in a different format be helpful?"

3. **Save the Document**:
   ```bash
   # Always save to outputs directory
   cp /home/claude/PRD_[Feature_Name].md /mnt/user-data/outputs/
   ```

## Quality Checklist

Before finalizing a PRD, ensure:

- [ ] Executive summary clearly explains the "what" and "why"
- [ ] All functional requirements have acceptance criteria
- [ ] User flows are documented with diagrams or steps
- [ ] Edge cases and error handling are addressed
- [ ] Data schemas are defined
- [ ] Technical implementation approach is clear
- [ ] Testing requirements are specified
- [ ] Success metrics are quantifiable
- [ ] Risks are identified with mitigations
- [ ] Future enhancements are noted but separate from MVP
- [ ] Code examples use real syntax and types
- [ ] Document is well-formatted with clear hierarchy
- [ ] Glossary defines domain-specific terms
- [ ] References to existing docs/code are accurate

## Examples of Good Requirements

**Good:**
```markdown
### F-001: Bulk CSV Upload
**Priority:** P0 (Critical)

**Description:**
Users can upload a CSV file containing multiple product listings. Each row represents one item with columns: title, description, category, condition, price_ebay, price_vinted, image_url.

**Acceptance Criteria:**
- [ ] Accepts CSV files up to 10MB
- [ ] Validates CSV headers match template exactly
- [ ] Processes up to 500 items per upload
- [ ] Shows progress bar during upload (every 10 items)
- [ ] Returns detailed error report if any items fail validation
- [ ] Allows user to download failed items as CSV for correction
- [ ] Successfully processed items are created as draft listings
- [ ] Process completes in < 30 seconds for 100 items

**Technical Notes:**
- Use streaming CSV parser to handle large files
- Process in batches of 50 to avoid timeout
- Store upload job in database with status tracking
- Send webhook notification on completion
```

**Bad:**
```markdown
### Bulk Upload
Users should be able to upload multiple items.
- Support CSV
- Make it fast
- Handle errors
```

## Advanced Techniques

### For Complex Features
- Create multiple PRDs (e.g., "PRD: Core Feature" + "PRD: Admin Interface")
- Use appendices for detailed technical specs
- Include architecture diagrams using mermaid
- Create a glossary for domain terms

### For Iterative Development
- Mark sections with version numbers (v1.0, v1.1)
- Use strikethrough for deprecated requirements
- Maintain a change log at the end
- Link related PRDs for context

### For API-Heavy Features
- Document all endpoints in OpenAPI/Swagger format
- Include request/response examples
- Specify authentication requirements
- Document rate limits and quotas

## Final Notes

Remember:
- **Interview thoroughly** - The better your questions, the better the PRD
- **Explore the codebase** - Context makes requirements realistic
- **Think like a PM** - Balance user needs, technical feasibility, and business value
- **Be specific** - Vague requirements lead to confusion and rework
- **Plan for failure** - Error handling is not optional
- **Document decisions** - Future teams will thank you

The goal is to create a PRD that an engineer can pick up and implement with minimal back-and-forth. When in doubt, add more detail rather than less.
