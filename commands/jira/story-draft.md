description: Comprehensive prompt for drafting user stories in Jira

## Variables

> Note: Format should be like "DTPTCON-123"

RESOURCE: $ARGUMENTS 
> Note: Can be a Jira Epic ID (format: "DTPTCON-123"), document reference, or other input. If not provided, attempt to infer from context or ask user to specify.

## Context

You are a Tech Lead within PayPal, and you want to create a well throughout Jira story and includes the following sections. Additional sections can be added, but they should be added underneath these main sections.

### Jira Content Format
> Note: These are the minimum requirements. Review the details in ai_docs/jira-details.md if available before creating any tickets.
* Description
* Additional Information
* Business Justification
* Acceptance Criteria - This should be in bullet format

## Prerequisites Validation

Before starting, verify:
- [ ] Atlassian MCP tools are accessible
- [ ] User has access to JIRA epic/project
- [ ] Understanding of epic context and requirements
- [ ] Access to relevant documentation and context

## MCP Tools

Use atlassian and smb-fs mcp servers as needed:
- **atlassian**: Get epic context (`jira_get_issue`), documentation (`confluence_get_page`)
- **smb-fs**: Get context from Slack channels (`slack_get_channel_history`, `slack_get_thread_replies`)

> If there is no access to this tools, tell me and ask me follow the [docs](https://paypal.atlassian.net/wiki/spaces/SMBFS/pages/2215246080/MCP+-+Open-Source+Servers).

## Steps

### 1. Resource Context Gathering
- If $RESOURCE is a Jira Epic: Use atlassian MCP tool to get JIRA epic details
- If $RESOURCE is a document: Read and analyze the provided document/reference
- If $RESOURCE is user input: Use the provided context directly
- Extract requirements, objectives, and constraints from the resource
- Review any linked documentation or architectural decisions
- **Error Handling**: If resource is inaccessible, ask user to provide context manually

### 2. Additional Context (Optional)
- Use smb-fs MCP tool for Slack conversations mentioned in epic
- Gather any relevant documentation from Confluence
- **Error Handling**: If additional tools are inaccessible, continue with available context

### 3. Additional Context and Requirements Analysis
- Analyze resource context and identify story scope
- If resource is a Jira Epic: Break down epic into logical user story components
- If resource is documentation: Extract relevant requirements and user scenarios
- If resource is user input: Clarify and structure the provided requirements
- Identify user personas and use cases from any resource type
- Note any technical constraints or dependencies
- Gather additional context from related documents, Slack conversations, or user clarifications as needed

### 4. Story Definition
- Define clear user story following "As a... I want... So that..." format
- Ensure story is appropriately sized (not too large for a sprint)
- Align with resource objectives and acceptance criteria
- Consider edge cases and error scenarios

### 5. Story Content Creation
Create comprehensive story content including:

#### A. Description
- Clear user story statement
- Background context and motivation
- Technical approach overview (if applicable)
- Dependencies and assumptions

#### B. Additional Information
- Technical implementation details
- Integration points and APIs
- Performance requirements
- Security considerations
- Accessibility requirements

#### C. Business Justification
- Business value and impact
- User experience improvements
- Technical debt reduction (if applicable)
- Risk mitigation

#### D. Acceptance Criteria
- Functional requirements in bullet format
- Non-functional requirements (performance, security)
- Edge cases and error handling
- Definition of done criteria

### 6. Quality Review
- Ensure story meets INVEST criteria (Independent, Negotiable, Valuable, Estimable, Small, Testable)
- Verify acceptance criteria are testable
- Check alignment with resource objectives
- Validate business value is clear

### 7. Output Generation
- Create story content in markdown format
- Include all required sections with clear formatting
- Add any relevant tags or labels
- Prepare for JIRA import

## Task

Think about the current context and draft a Jira story based on the provided resource context and requirements.

## Output

Output the results in tmp folder in project root with filename JIRA-{simple summary}.md

## Error Recovery Strategies

- **Resource Access Issues**: Ask user to provide resource details manually
- **Missing Context**: Request additional information from user
- **Scope Too Large**: Break into multiple smaller stories
- **Unclear Requirements**: Ask clarifying questions before proceeding

## Success Criteria

- [ ] Story follows proper user story format
- [ ] All required sections are complete and detailed
- [ ] Acceptance criteria are clear and testable
- [ ] Story aligns with resource objectives
- [ ] Business value is clearly articulated
- [ ] Technical considerations are addressed
- [ ] Story is appropriately sized for development

## Story Quality Guidelines

### User Story Best Practices
- **Clear Value**: Every story should deliver clear business value
- **User-Focused**: Written from user perspective with clear persona
- **Testable**: Acceptance criteria should be verifiable
- **Independent**: Story should be developable independently
- **Negotiable**: Details can be discussed and refined
- **Estimable**: Story scope should be clear enough to estimate

### Acceptance Criteria Standards
- **Specific**: Clear, unambiguous requirements
- **Measurable**: Quantifiable where applicable
- **Achievable**: Realistic within technical constraints
- **Relevant**: Aligned with story objectives
- **Testable**: Can be verified through testing