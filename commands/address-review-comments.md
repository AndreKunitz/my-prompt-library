description: Address code review comments on a PR systematically

## Variables

JIRA_STORY: $JIRA_STORY
PR_NUMBER: ${PR_NUMBER}
COMMIT_STRATEGY: ${COMMIT_STRATEGY:-single}  # single, individual, or ask
PREFERRED_REMOTE: ${PREFERRED_REMOTE:-PostPurchase-R}

> Note: JIRA_STORY format should be like "DTPTCON-123"
> If JIRA_STORY is not provided, attempt to infer from current branch name or PR title
> If PR_NUMBER is not provided, attempt to find PR associated with current branch
> COMMIT_STRATEGY: single (one commit for all fixes), individual (separate commits), ask (confirm each time)
> PREFERRED_REMOTE: Remote to push changes to. Defaults to PostPurchase-R. Will ask user to choose from available remotes and remember choice for session if not specified.

## Context

You are an expert senior software engineer within PayPal, addressing code review feedback on a PR. Your goal is to systematically address each review comment while maintaining code quality and following team standards. Refer to CLAUDE.md file for project-specific instructions.

## Prerequisites Validation

Before starting, verify:
- [ ] Current directory is a git repository: `git status`
- [ ] GitHub CLI is authenticated: `gh auth status`
- [ ] Working on correct branch with pending PR
- [ ] Atlassian and SMB-FS MCP tools are accessible
- [ ] Maven/build tools are available for testing

## MCP Tools

Use atlassian and smb-fs mcp servers as needed:
- **atlassian**: Get JIRA context (`jira_get_issue`), documentation links (`confluence_get_page`)
- **smb-fs**: Get Slack conversation context (`slack_get_channel_history`, `slack_get_thread_replies`)

## Steps

### 1. Setup and Validation
**Objective**: Establish context and verify PR accessibility

#### A. JIRA Story Analysis
- Use atlassian MCP tool to get JIRA issue details: `jira_get_issue` with $JIRA_STORY
- Extract key requirements, acceptance criteria, and any linked documentation
- **Error Handling**: If JIRA inaccessible, ask user to provide story summary

#### B. PR Identification and Validation
- If $PR_NUMBER not provided:
  - Find PR for current branch: `gh pr view --json number,title,url`
  - If multiple PRs or none found, ask user to specify PR number
- Validate PR exists and is accessible: `gh pr view $PR_NUMBER`
- Confirm PR title contains $JIRA_STORY or ask user to verify correct PR

#### C. Current Branch Verification
- **Remote Selection**: If $PREFERRED_REMOTE not set:
  - List available remotes: `git remote -v`
  - Ask: "Which remote to use for pushing changes? Available: [list remotes]. Default: PostPurchase-R"
  - Remember user's choice for this session
- Ensure on correct feature branch: `git branch --show-current`
- If not on PR branch, ask user: "Switch to PR branch? Current: [branch], PR branch: [pr_branch]"
- Sync with remote if needed: `git pull $PREFERRED_REMOTE [branch_name]`

### 2. Review Comment Analysis
**Objective**: Systematically catalog and understand all review feedback

#### A. Fetch Review Comments
- Get structured review data: `gh pr view $PR_NUMBER --json reviews`
- Extract review comments with context (file, line, comment text)
- Get general PR comments: `gh pr view $PR_NUMBER --json comments`

#### B. Comment Categorization
Organize comments by:
- **Critical**: Security issues, bugs, breaking changes
- **Major**: Design improvements, performance issues, maintainability
- **Minor**: Style, naming, documentation
- **Questions**: Requests for clarification or explanation
- **Suggestions**: Optional improvements

#### C. Comment Context Gathering
For each comment:
- Identify affected file and line numbers
- Read surrounding code context
- If comment references external docs/links, fetch additional context using appropriate tools
- Note any conflicting feedback between reviewers

### 3. Implementation Planning
**Objective**: Create systematic plan to address all feedback

#### A. Dependency Analysis
- Identify comments that depend on others (e.g., refactoring affects multiple files)
- Group related changes that should be implemented together
- Flag potential conflicts between different reviewer suggestions

#### B. Implementation Sequence
- **Phase 1**: Critical security/bug fixes
- **Phase 2**: Major architectural/design changes
- **Phase 3**: Minor improvements and style fixes
- **Phase 4**: Documentation and clarification responses

#### C. Change Impact Assessment
For each planned change:
- Estimate complexity and time required
- Identify potential side effects
- Note testing requirements
- Plan rollback strategy if needed

### 4. User Collaboration and Approval
**Objective**: Get user buy-in for implementation approach

#### A. Present Comprehensive Plan
Show user:
- Summary of all review comments by category
- Proposed implementation sequence
- Estimated effort and complexity
- Any conflicts or concerns identified

#### B. Individual Comment Review
For each comment (starting with Critical):
- **Show**: Original comment with file/line context
- **Propose**: Specific solution approach
- **Ask**: "Approve this approach? Alternative suggestions?"
- **Wait**: For explicit user approval before proceeding
- **Document**: User's decision (approve/modify/reject/defer)

#### C. Conflict Resolution
If conflicting reviewer feedback:
- Present both perspectives clearly
- Suggest resolution approach
- Ask user to choose direction or request reviewer clarification
- Document resolution for PR response

### 5. Implementation Phase
**Objective**: Implement approved changes systematically

#### A. Setup Change Tracking
- Create checklist of approved changes
- Track completion status as you work
- Note any implementation challenges encountered

#### B. Implement Changes by Priority
For each approved change:
- **Before**: Show current code
- **Implement**: Make the change following project patterns
- **Validate**: Ensure change compiles and basic functionality works
- **Test**: Run relevant tests if quick validation possible
- **Mark Complete**: Update tracking checklist

#### C. Handle Implementation Issues
If implementation problems arise:
- Document the specific issue
- Present alternative approaches
- Ask user for guidance: continue, modify approach, or defer
- Update implementation plan accordingly

### 6. Testing and Validation
**Objective**: Ensure all changes work correctly together

#### A. Comprehensive Testing
- Run full test suite: `mvn test` (or appropriate command)
- If tests fail: **STOP** and present failure details to user
- Run linting/style checks: `mvn checkstyle:check`
- Fix any test failures iteratively

#### B. Integration Validation
- Build project successfully: `mvn clean install`
- Test key functionality affected by changes
- Verify no regressions introduced
- **Error Recovery**: If integration issues found, present options to user

#### C. Code Quality Review
- Self-review all changes for consistency
- Ensure changes follow existing patterns
- Verify documentation is updated if needed
- Check for any TODO comments or incomplete implementations

### 7. Response Preparation
**Objective**: Prepare comprehensive response to reviewers

#### A. Change Summary Creation
- Document what was changed for each comment
- Note any alternative approaches taken
- Explain rationale for design decisions
- Identify any suggestions not implemented and why

#### B. Reviewer Communication Strategy
- Prepare response for each individual comment
- Address questions with clear explanations
- Thank reviewers for valuable feedback
- Request re-review of specific areas if needed

### 8. Commit and Push Strategy
**Objective**: Commit changes appropriately based on strategy

#### A. Commit Strategy Execution
Based on $COMMIT_STRATEGY:

**Single Commit**:
- Stage all changes: `git add .`
- Create comprehensive commit message: "Address code review feedback"
- Include bullet points of major changes addressed
- Commit: `git commit -m "[message]"`

**Individual Commits**:
- For each logical group of changes:
  - Stage related files: `git add [files]`
  - Create specific commit message: "[specific change description]"
  - Commit: `git commit -m "[message]"`

**Ask Strategy**:
- Present commit options for each change group
- Let user decide on commit granularity
- Execute user's preferred approach

#### B. Pre-Push Validation
- Show staged changes: `git diff --cached`
- Confirm commit messages are descriptive
- Ask: "Push changes and notify reviewers?"
- **Final Check**: Verify all review comments have been addressed

### 9. Follow-up Actions
**Objective**: Complete the review response cycle

#### A. Push and Notify
- Push changes: `git push $PREFERRED_REMOTE [branch]`
- Verify push successful
- Comment on PR with summary of changes made
- Request re-review from original reviewers

#### B. Documentation Update
- Update any tracking documents
- Note any follow-up actions needed
- Document lessons learned for future reviews

## Error Recovery Strategies

- **JIRA Issues**: Continue with user-provided context if MCP fails
- **PR Access Issues**: Verify permissions and authentication
- **Git Conflicts**: Guide user through resolution process
- **Test Failures**: Present clear error details and suggested fixes
- **Build Issues**: Identify specific problems and provide solutions
- **Reviewer Conflicts**: Facilitate communication and decision-making

## Success Criteria

- [ ] All review comments systematically addressed or documented why not
- [ ] User approved each significant change before implementation
- [ ] All tests pass after changes
- [ ] Code follows project conventions and quality standards
- [ ] Clear commit history with descriptive messages
- [ ] Reviewers notified with comprehensive response
- [ ] No regressions introduced by changes

## Best Practices

### Communication Guidelines
- Always explain rationale for significant design decisions
- Be respectful and appreciative of reviewer feedback
- Ask for clarification rather than guessing reviewer intent
- Provide context for why alternative approaches were chosen

### Code Quality Maintenance
- Maintain consistency with existing codebase patterns
- Ensure changes don't introduce technical debt
- Update tests and documentation as needed
- Follow team coding standards and conventions