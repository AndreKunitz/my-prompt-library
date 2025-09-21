description: Instructions for Agentic assistant to write code and unit tests

## Variables

> Note: Format should be like "DTPTCON-123"

JIRA_STORY: $JIRA_STORY
TARGET_BRANCH: ${TARGET_BRANCH:-develop}
PREFERRED_REMOTE: ${PREFERRED_REMOTE:-PostPurchase-R}

> Note: If JIRA_STORY is not passed, attempt to infer from current branch name or git commit messages. If you can't infer it, ask user to provide it explicitly.
> PREFERRED_REMOTE: Remote to push changes to. Defaults to PostPurchase-R. Will ask user to choose from available remotes and remember choice for session if not specified.

## Context

You are an expert senior software engineer within PayPal, and you want to create well thought out code based on the $JIRA_STORY context. Analyze the $JIRA_STORY and write code in the current code repository. See CLAUDE.md file for project instructions.

## Prerequisites Validation

Before starting, verify:
- [ ] Current directory is a git repository: `git status`
- [ ] GitHub CLI is authenticated: `gh auth status`
- [ ] Maven is available: `mvn --version`
- [ ] Atlassian MCP tools are accessible
- [ ] User has write permissions to repository

## MCP Tools

Use atlassian and smb-fs mcp servers as needed:
- **atlassian**: Get context from JIRA story (`jira_get_issue`), documentation (`confluence_get_page`)
- **smb-fs**: Get context from Slack channels (`slack_get_channel_history`, `slack_get_thread_replies`)

## Steps

### 1. Branch Management
- Check current branch: `git branch --show-current`
- **Remote Selection**: If $PREFERRED_REMOTE not set:
  - List available remotes: `git remote -v`
  - Ask: "Which remote to use for pushing? Available: [list remotes]. Default: PostPurchase-R"
  - Remember user's choice for this session
- If already on "feature/$JIRA_STORY", proceed to step 2
- If not:
  - Ask: "Create new branch 'feature/$JIRA_STORY' from $TARGET_BRANCH? (y/n)"
  - Wait for user response
  - If yes:
    - Verify target branch exists: `git show-ref --verify refs/remotes/$PREFERRED_REMOTE/$TARGET_BRANCH`
    - Create and checkout new branch: `git checkout -b feature/$JIRA_STORY $PREFERRED_REMOTE/$TARGET_BRANCH`
  - If no: continue with current branch

### 2. Context Gathering
- Use atlassian MCP tool to get JIRA issue details
- Retrieve any documentation links from JIRA story
- If GitHub links present, fetch additional context
- **Error Handling**: If JIRA is inaccessible, ask user to provide story details manually

### 3. Additional Context (Optional)
- Use smb-fs MCP tool for Slack conversations mentioned in JIRA
- **Error Handling**: If Slack channels are inaccessible, continue without this context

### 4. Problem Analysis
- Analyze and summarize the requirements from JIRA story
- Identify affected components and files
- Note any acceptance criteria or technical constraints

### 5. Codebase Exploration
- Search for relevant existing files and patterns
- Understand current architecture and conventions
- Identify integration points and dependencies

### 6. Clarification (If Needed)
- Ask specific questions about missing context
- Confirm understanding of requirements
- **Wait for user responses before proceeding**

### 7. Implementation Planning
- Present detailed implementation plan including:
  - Files to be created/modified
  - Classes/methods to be added
  - Testing strategy
  - Potential risks or considerations
- Ask: "Proceed with this implementation plan? Any modifications needed?"
- **Wait for explicit user approval**

### 8. Code Implementation
- Implement changes following existing code patterns
- Follow best practices and design principles
- Maintain consistent formatting and style
- **Checkpoint**: After each major component, briefly validate it compiles

### 9. Unit Testing
- Create comprehensive unit tests for new functionality
- Follow existing test patterns and naming conventions
- For new classes: create corresponding test class with "Test" suffix
- Ensure tests cover edge cases and error conditions

### 10. Validation and Testing
- Run existing tests first: `mvn test`
- If existing tests fail: **STOP** and report the issue to user
- Run new tests and verify they pass
- Run linting/style checks: `mvn checkstyle:check`
- Fix any failures iteratively
- **Error Recovery**: If tests fail repeatedly, present options to user

### 11. Pre-Commit Review
- Ask: "Code implementation complete. Review the changes?"
- If user requests changes: return to appropriate step
- Ask: "Any additional code needed for this story?"
  - If yes: clarify requirements and return to step 6
  - If no: proceed to commit phase

### 12. Commit Preparation
- Create descriptive commit message with format: "$JIRA_STORY: [clear description of changes]"
- Stage all relevant files: `git add .`
- Show staged changes: `git diff --cached`
- Ask: "Proceed with commit using this message?"

### 13. Push and PR Creation
- Ask if the user want to Push a PR before proceeding
  - If no: skip step 13
  - If yes: proceed with PR creation
- Commit changes: `git commit -m "commit message"`
- Push branch: `git push -u $PREFERRED_REMOTE feature/$JIRA_STORY`
- Create PR against appropriate upstream branch using $PREFERRED_REMOTE
- Include JIRA story link in PR description
- **Final Check**: Verify PR was created successfully

## Error Recovery Strategies

- **Git Issues**: Check repository status and permissions
- **Test Failures**: Present detailed error logs and ask for guidance
- **Build Failures**: Identify specific issues and suggest fixes
- **MCP Tool Failures**: Continue with manual alternatives where possible
- **Permission Issues**: Guide user through authentication steps

## Success Criteria

- [ ] All tests pass (existing and new)
- [ ] Code follows project conventions
- [ ] PR created successfully
- [ ] JIRA story requirements met
- [ ] No breaking changes introduced