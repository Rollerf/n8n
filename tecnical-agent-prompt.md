# Design Document Creation Prompt

## Prompt Template (Role, Task, Context, Expectations)

```markdown
Role: Act as an experienced n8n architect and senior developer. You are responsible for designing robust, maintainable environments for n8n development and production, and for creating efficient deployment pipelines.

Task: Produce a design and configuration for:
- Local development environment (easy setup, debugging, testing)
- Production environment (secure, scalable, reliable)
- CI/CD pipelines for seamless deployment from local to production

Required Context:
- Business purpose and goals: [BUSINESS_GOALS]
- Stakeholders and users: [USERS_AND_STAKEHOLDERS]
- Functional scope overview: [FEATURE_DESCRIPTION]
- Constraints (technical, compliance, budget, timeline): [CONSTRAINTS]
- Existing systems and integration points: [INTEGRATIONS]
- Non-functional requirements (performance, security, availability, privacy): [NFRS]
- Assumptions and out-of-scope items: [ASSUMPTIONS_AND_OOS]

Expectations and Goals:
1) Executive Summary
   - Overview of n8n environments and deployment objectives

2) Problem Statement
   - Current pain points in environment setup and deployment

3) Proposed Solution (High-Level)
   - Architecture for local and production environments
   - Key technology choices (e.g., Docker, Kubernetes, cloud provider)
   - CI/CD pipeline overview

4) Detailed Design
   4.1 Environment Architecture
   - Component diagram (text description), responsibilities, data flow
   4.2 Configuration Management
   - Secrets, environment variables, config files
   4.3 CI/CD Pipeline Design
   - Steps, tools (e.g., GitHub Actions, Jenkins), rollback strategy
   4.4 Integration Points
   - External services, databases, authentication

5) Technical Specifications
   - Performance, security, error handling, monitoring/logging

6) Implementation Plan
   - Phases, milestones, timeline, resource needs

7) Testing Strategy
   - Environment validation, pipeline tests, acceptance criteria

8) Deployment and Operations
   - Environments, CI/CD details, runbooks, SRE practices

9) Risks and Mitigations
   - Technical/business risks, mitigations

10) Success Metrics
   - KPIs, monitoring, post-launch evaluation

11) Future Considerations
   - Scalability, maintainability, tech debt

12) Appendices
   - Glossary, references, diagrams, ADRs

Authoring Guidelines:
- Be specific, justify decisions, cite trade-offs
- Consider edge cases and failure modes
- Provide code/pseudo-code examples for configs and pipelines
- Call out migration and backward compatibility
- Use clear headings and bullet lists