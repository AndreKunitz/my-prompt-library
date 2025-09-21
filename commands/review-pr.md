description: Conduct a comprehensive and systematic code review of a PR

## Variables

JIRA_STORY: $JIRA_STORY
PR_NUMBER: ${PR_NUMBER}
REVIEW_DEPTH: ${REVIEW_DEPTH:-standard}  # quick, standard, thorough
FOCUS_AREAS: ${FOCUS_AREAS:-all}  # security, performance, design, style, or all

> Note: JIRA_STORY format should be like "DTPTCON-123"
> If JIRA_STORY is not provided, attempt to infer from PR title or ask user
> If PR_NUMBER is not provided, attempt to find PR from JIRA_STORY context
> REVIEW_DEPTH: quick (30min), standard (1-2hr), thorough (2-4hr)
> FOCUS_AREAS: Comma-separated list or 'all' for comprehensive review

## Context

You are an expert senior software engineer within PayPal conducting a professional code review. Your goal is to provide constructive, actionable feedback that improves code quality, security, and maintainability while mentoring the developer. Refer to CLAUDE.md file for project-specific instructions and standards.

## Prerequisites Validation

Before starting, verify:
- [ ] PR exists and is accessible: `gh pr view $PR_NUMBER`
- [ ] JIRA story context is available
- [ ] Atlassian and SMB-FS MCP tools are accessible
- [ ] Understanding of project architecture and standards
- [ ] Sufficient time allocated for review depth selected

## MCP Tools

Use atlassian and smb-fs mcp servers as needed:
- **atlassian**: Get JIRA context (`jira_get_issue`), documentation links (`confluence_get_page`)
- **smb-fs**: Get Slack conversation context (`slack_get_channel_history`, `slack_get_thread_replies`)

## Review Framework

### 1. Pre-Review Setup
**Objective**: Establish context and scope for comprehensive review

#### A. Story and Requirements Analysis
- Use atlassian MCP tool to get JIRA issue details: `jira_get_issue` with $JIRA_STORY
- Extract requirements, acceptance criteria, and technical constraints
- Review any linked documentation or architectural decisions
- **Error Handling**: If JIRA inaccessible, ask user to provide story context

#### B. PR Scope Assessment
- Get PR details: `gh pr view $PR_NUMBER --json title,body,additions,deletions,changedFiles`
- Review PR description and self-assessment by author
- Identify scope: new feature, bug fix, refactoring, etc.
- Note size and complexity (lines changed, files affected)

#### C. Change Impact Analysis
- Identify affected components and systems
- Check for breaking changes or API modifications
- Review dependencies and integration points
- Assess potential performance implications

### 2. Code Quality Assessment
**Objective**: Evaluate code quality across multiple dimensions

#### A. Security Review (Priority: CRITICAL)
**Focus Areas:**
- **Input Validation**: All user inputs properly validated and sanitized
- **Authentication/Authorization**: Proper access controls implemented
- **Secrets Management**: No hardcoded secrets, keys, or passwords
- **SQL Injection**: Parameterized queries, no string concatenation
- **XSS Prevention**: Proper output encoding and validation
- **CSRF Protection**: Anti-CSRF tokens where applicable
- **Dependency Security**: No known vulnerable dependencies

**Evaluation Criteria:**
- Rate security posture: SECURE / NEEDS_ATTENTION / VULNERABLE
- Document specific security concerns with file/line references
- Provide remediation guidance for any issues found

#### B. Performance Review (Priority: HIGH)
**Focus Areas:**
- **Database Queries**: Efficient queries, proper indexing, N+1 prevention
- **Memory Usage**: Memory leaks, large object retention
- **Algorithm Efficiency**: Time/space complexity analysis
- **Caching Strategy**: Appropriate use of caching mechanisms
- **Resource Management**: Proper cleanup of resources
- **Async/Threading**: Correct concurrency patterns

**Evaluation Criteria:**
- Identify performance bottlenecks
- Suggest optimization opportunities
- Flag potential scalability issues

#### C. Design and Architecture Review (Priority: HIGH)
**Focus Areas:**
- **SOLID Principles**: Single Responsibility, Open/Closed, etc.
- **Design Patterns**: Appropriate pattern usage and implementation
- **Separation of Concerns**: Clear boundaries between layers
- **Dependency Management**: Proper dependency injection and coupling
- **Error Handling**: Comprehensive and consistent error management
- **API Design**: RESTful principles, consistent interfaces

**Evaluation Criteria:**
- Assess architectural alignment with existing patterns
- Identify design anti-patterns or code smells
- Suggest refactoring opportunities for better maintainability

#### D. Code Maintainability Review (Priority: MEDIUM)
**Focus Areas:**
- **Readability**: Clear, self-documenting code
- **Naming Conventions**: Descriptive and consistent naming
- **Function/Class Size**: Appropriate granularity
- **Code Duplication**: DRY principle adherence
- **Comments and Documentation**: Meaningful documentation
- **Test Coverage**: Adequate unit and integration tests

**Evaluation Criteria:**
- Rate maintainability: EXCELLENT / GOOD / NEEDS_IMPROVEMENT / POOR
- Identify areas that will be difficult to maintain or extend
- Suggest improvements for long-term code health

#### E. Style and Standards Compliance (Priority: LOW)
**Focus Areas:**
- **Coding Standards**: Team/project style guide adherence
- **Formatting**: Consistent indentation, spacing, organization
- **Import Management**: Clean, organized imports
- **Dead Code**: Unused variables, methods, or imports
- **TODO/FIXME**: Appropriate use of temporary markers

### 3. Testing Strategy Review
**Objective**: Ensure comprehensive and effective test coverage

#### A. Test Coverage Analysis
- Review unit tests for new functionality
- Check integration test coverage for affected components
- Verify edge cases and error conditions are tested
- Assess test quality and maintainability

#### B. Test Strategy Evaluation
- Ensure tests follow AAA pattern (Arrange, Act, Assert)
- Check for proper mocking and isolation
- Verify test data setup and cleanup
- Review test naming and documentation

### 4. Business Logic Review
**Objective**: Validate implementation meets business requirements

#### A. Requirements Alignment
- Verify implementation matches JIRA story requirements
- Check acceptance criteria fulfillment
- Validate business rules implementation
- Ensure edge cases are properly handled

#### B. User Experience Impact
- Review impact on user workflows
- Check for accessibility considerations
- Validate error messages and user feedback
- Assess performance impact on user experience

### 5. Integration and Compatibility Review
**Objective**: Ensure changes integrate properly with existing systems

#### A. API Compatibility
- Check for breaking changes in public interfaces
- Verify backward compatibility requirements
- Review versioning strategy if applicable
- Validate contract adherence

#### B. Database Impact
- Review schema changes and migration scripts
- Check for potential data loss or corruption risks
- Validate indexing strategy
- Assess impact on existing queries and reports

#### C. Deployment Considerations
- Review configuration changes required
- Check for feature flags or gradual rollout needs
- Validate monitoring and alerting coverage
- Assess rollback strategy

### 6. Review Findings Compilation
**Objective**: Organize and prioritize all feedback systematically

#### A. Finding Categorization
Organize all findings by:
- **BLOCKER**: Must fix before merge (security vulnerabilities, breaking changes)
- **CRITICAL**: Should fix before merge (major design flaws, performance issues)
- **MAJOR**: Important improvements (maintainability, best practices)
- **MINOR**: Nice-to-have improvements (style, optimization)
- **SUGGESTION**: Optional enhancements (alternative approaches)

#### B. Detailed Finding Documentation
For each finding:
- **File/Line Reference**: Exact location with GitHub permalink
- **Issue Description**: Clear explanation of the problem
- **Impact Assessment**: Why this matters (security, performance, maintainability)
- **Recommended Solution**: Specific actionable guidance
- **Alternative Approaches**: Other potential solutions if applicable
- **Learning Opportunity**: Educational context for the developer

#### C. Positive Feedback Identification
- Highlight excellent code examples
- Acknowledge good design decisions
- Recognize proper implementation of best practices
- Encourage continued good patterns

### 7. Review Report Generation
**Objective**: Create comprehensive, actionable review feedback

#### A. Executive Summary
- **Overall Assessment**: APPROVE / APPROVE_WITH_CHANGES / REQUEST_CHANGES / REJECT
- **Key Strengths**: Top 2-3 positive aspects of the PR
- **Critical Issues**: Must-fix items before merge
- **Improvement Areas**: Opportunities for enhancement
- **Risk Assessment**: Potential risks and mitigation strategies

#### B. Detailed Review Comments
- **File-Level Comments**: General feedback for specific files
- **Line-Level Comments**: Specific code suggestions with context
- **General Comments**: Overall architectural or strategic feedback
- **Questions**: Areas needing clarification from the author

#### C. Action Items and Follow-up
- **Pre-Merge Requirements**: What must be addressed before approval
- **Future Improvements**: Items for subsequent PRs or refactoring
- **Documentation Needs**: Updates to docs, runbooks, or team knowledge
- **Learning Opportunities**: Suggested reading or training for the team

### 8. Review Delivery and Follow-up
**Objective**: Provide feedback in a constructive and supportive manner

#### A. Constructive Communication
- Frame feedback positively and educationally
- Provide rationale for all suggestions
- Offer specific examples and alternatives
- Acknowledge the effort and positive aspects

#### B. Priority Guidance
- Clearly communicate what must be fixed vs. suggestions
- Provide timeline expectations for addressing feedback
- Offer assistance or pairing for complex changes
- Set expectations for re-review process

#### C. Mentoring Opportunities
- Explain the "why" behind best practices
- Share relevant resources or documentation
- Suggest learning opportunities
- Encourage questions and discussion

## Review Quality Checklist

### Before Submitting Review
- [ ] All critical security and safety issues identified
- [ ] Performance implications thoroughly considered
- [ ] Design alignment with project architecture verified
- [ ] Test coverage adequacy assessed
- [ ] Business requirements fulfillment confirmed
- [ ] Feedback is constructive and actionable
- [ ] Positive aspects acknowledged
- [ ] Learning opportunities provided

### Review Communication Standards
- [ ] Professional and respectful tone maintained
- [ ] Specific examples and locations provided
- [ ] Clear distinction between must-fix and suggestions
- [ ] Educational context included for complex topics
- [ ] Offer of assistance or clarification provided

## Error Recovery Strategies

- **JIRA/MCP Issues**: Continue with available context, ask user for missing information
- **PR Access Issues**: Verify permissions and authentication, guide user through access
- **Large PR Challenges**: Break review into logical sections, prioritize critical areas
- **Complex Technical Issues**: Seek additional expertise or schedule code review session
- **Time Constraints**: Adjust review depth based on available time and PR complexity

## Success Criteria

- [ ] Comprehensive review completed across all relevant dimensions
- [ ] All critical security and safety issues identified
- [ ] Constructive feedback provided with specific guidance
- [ ] Business requirements alignment verified
- [ ] Test coverage adequacy assessed
- [ ] Clear action items and priorities communicated
- [ ] Positive aspects and learning opportunities highlighted
- [ ] Professional and supportive tone maintained throughout

## Review Best Practices

### Code Review Philosophy
- **Collaborative**: Reviews are discussions, not judgments
- **Educational**: Every review is a learning opportunity
- **Quality-Focused**: Prioritize code quality and maintainability
- **Respectful**: Acknowledge effort and provide constructive feedback
- **Thorough**: Balance thoroughness with practicality and timelines

### Effective Review Techniques
- **Ask Questions**: When uncertain, ask for clarification rather than assume
- **Provide Examples**: Show better alternatives when suggesting changes
- **Consider Context**: Understand constraints and trade-offs made by the author
- **Think Long-term**: Consider maintenance and evolution of the codebase
- **Share Knowledge**: Use reviews to spread best practices and patterns