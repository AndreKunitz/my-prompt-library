description: Instructions for Agentic assistant to write functional tests (integrated and isolated)

## Variables

> Note: Format should be like "DTPTCON-123"

JIRA_STORY: $JIRA_STORY
TARGET_BRANCH: ${TARGET_BRANCH:-develop}
PREFERRED_REMOTE: ${PREFERRED_REMOTE:-PostPurchase-R}
TEST_TYPE: ${TEST_TYPE:-both}  # integrated, isolated, or both
SERVICE_NAME: ${SERVICE_NAME}  # Auto-determined from git repo if not specified

> Note: If JIRA_STORY is not passed, attempt to infer from current branch name or git commit messages. If you can't infer it, ask user to provide it explicitly.
> PREFERRED_REMOTE: Remote to push changes to. Defaults to PostPurchase-R. Will ask user to choose from available remotes and remember choice for session if not specified.
> TEST_TYPE: Type of functional tests to write - integrated (with real dependencies), isolated (mocked dependencies), or both
> SERVICE_NAME: If not specified, will be auto-determined from current git repository name (e.g., packagetrackingserv from repo)

## Context

You are an expert senior software engineer within PayPal's Post Purchase Products team writing comprehensive functional tests for our components. Your goal is to create thorough functional test coverage for features described in $JIRA_STORY, focusing on configuration validation, API contract testing, and business rule verification.

## Prerequisites Validation

Before starting, verify:
- [ ] Current directory is a git repository: `git status`
- [ ] Determine service name from git repo if $SERVICE_NAME not set: `git remote get-url origin`
- [ ] Locate and verify FunctionalTests project directory exists
- [ ] GitHub CLI is authenticated: `gh auth status`
- [ ] Maven is available for running tests: `mvn --version`
- [ ] Atlassian and SMB-FS MCP tools are accessible
- [ ] Understanding of service API endpoints and business logic

## MCP Tools

Use atlassian and smb-fs mcp servers as needed:
- **atlassian**: Get JIRA context (`jira_get_issue`), documentation links (`confluence_get_page`)
- **smb-fs**: Get Slack conversation context (`slack_get_channel_history`, `slack_get_thread_replies`)

## Steps

### 1. Service and Branch Setup
- **Service Name Detection**: If $SERVICE_NAME not set:
  - Get git remote URL: `git remote get-url origin`
  - Extract service name from repo URL (e.g., packagetrackingserv from .../packagetrackingserv.git)
  - Confirm with user: "Detected service: [service_name]. Correct? (y/n)"
- **Remote Selection**: If $PREFERRED_REMOTE not set:
  - List available remotes: `git remote -v`
  - Ask: "Which remote to use for pushing? Available: [list remotes]. Default: PostPurchase-R"
  - Remember user's choice for this session
- Check current branch: `git branch --show-current`
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
- Extract functional requirements, API changes, and acceptance criteria
- Identify specific endpoints, business logic, or features to test
- Review any test scenarios or edge cases mentioned in the story
- **Error Handling**: If JIRA inaccessible, ask user to provide story details manually

### 3. Additional Context (Optional)
- Use smb-fs MCP tool for Slack conversations mentioned in JIRA
- Gather context about testing approaches and patterns from team discussions
- **Error Handling**: If Slack channels are inaccessible, continue without this context

### 4. Functional Test Analysis
- Analyze and summarize the functional testing requirements from JIRA story
- Identify API endpoints to test (product endpoints, variant endpoints, configuration endpoints)
- Determine test scenarios: happy path, edge cases, error conditions
- Map business logic validation requirements
- Identify integration points and dependencies (UCP, external services)

### 5. Test Project Exploration
- **Locate FunctionalTests Directory**: Search for patterns like:
  - `[SERVICE_NAME]FunctionalTests/`
  - `functionalTests/`
  - `functional-tests/`
  - `*FunctionalTest*/`
- Search for existing test patterns and frameworks used
- Identify test base classes, utilities, and helper methods
- Review existing test data setup and configuration patterns
- Understand project structure and naming conventions

### 6. Test Strategy Planning
- **Integrated Tests** (if $TEST_TYPE includes integrated):
  - Tests that use real external dependencies
  - Focus on end-to-end workflows and API contract validation
  - Use actual database, external services where appropriate
  
- **Isolated Tests** (if $TEST_TYPE includes isolated):
  - Tests with mocked dependencies
  - Focus on business logic validation and error handling
  - Mock external services, databases, and third-party APIs
  
- **Test Coverage Areas**:
  - API endpoint testing (request/response validation)
  - Configuration validation and policy resolution
  - Hierarchy inheritance and override testing
  - Business rule validation
  - Error handling and edge cases
  - Data validation and transformation
  - Integration with external systems (UCP, clients)
  - Version compatibility and schema validation
  - Performance and load characteristics (if applicable)

### 7. Clarification (If Needed)
- Ask specific questions about:
  - Test data requirements and setup
  - Specific API endpoints or business rules to focus on
  - Environmental dependencies and configurations
  - Performance or load testing requirements
- **Wait for user responses before proceeding**

### 8. Implementation Planning
- Present detailed test implementation plan including:
  - Test classes to be created/modified
  - Test methods and scenarios to implement
  - Test data setup and configuration requirements
  - Mock strategies and integration approaches
  - Expected test coverage and validation points
- Ask: "Proceed with this functional test plan? Any modifications needed?"
- **Wait for explicit user approval**

### 9. Functional Test Implementation
- **Navigate to FunctionalTests Project**: Change to discovered FunctionalTests directory
- **Follow Existing Patterns**: Use established test frameworks and base classes
- **Implement Test Classes** based on approved plan:

#### A. Integrated Functional Tests
- Create or extend test classes for real integration scenarios
- Use actual service configurations and dependencies
- Implement end-to-end test workflows
- Validate API contracts and data flows
- Test actual database interactions and external service calls

#### B. Isolated Functional Tests  
- Create or extend test classes with mocked dependencies
- Mock external services, databases, and third-party APIs
- Focus on business logic validation
- Test error handling and edge cases
- Validate data transformations and business rules

#### C. Test Implementation Best Practices
- Follow AAA pattern (Arrange, Act, Assert)
- Use descriptive test method names
- Implement proper test data setup and cleanup
- Add meaningful assertions and error messages
- Include both positive and negative test scenarios
- **Checkpoint**: After each test class, verify tests compile and basic structure is correct

### 10. Test Data and Configuration
- Create or update test data files as needed
- Configure test-specific properties and environments
- Set up mock configurations for isolated tests
- Ensure test data follows existing patterns and conventions
- Validate test data doesn't conflict with existing tests

### 11. Test Validation and Execution
- Run new functional tests individually: `mvn test -Dtest=TestClassName`
- Verify all new tests pass independently
- Run integrated test suite to check for conflicts: `mvn test`
- If tests fail: **STOP** and present failure details to user
- Fix any test failures iteratively
- **Error Recovery**: If tests fail repeatedly, present options to user

### 12. Test Quality Review
- Self-review all test implementations for:
  - Test coverage completeness
  - Proper assertion usage
  - Clear test documentation and naming
  - Adherence to existing test patterns
  - Appropriate use of mocking vs real dependencies
- Verify tests are maintainable and understandable
- Check for any TODO comments or incomplete implementations

### 13. Pre-Commit Review
- Ask: "Functional test implementation complete. Review the changes?"
- If user requests changes: return to appropriate step
- Ask: "Any additional test scenarios needed for this story?"
- If yes: clarify requirements and return to step 6
- If no: proceed to commit phase

### 14. Commit Preparation
- Navigate back to main service directory if needed
- Create descriptive commit message with format: "$JIRA_STORY: Add functional tests for [feature description]"
- Stage all relevant test files in FunctionalTests directory
- Show staged changes: `git diff --cached`
- Ask: "Proceed with commit using this message?"

### 15. Push and PR Creation
- Ask if the user want to Push a PR before proceeding
  - If no: skip step 15
  - If yes: proceed with PR creation
- Commit changes: `git commit -m "commit message"`
- Push branch: `git push -u $PREFERRED_REMOTE feature/$JIRA_STORY`
- Create PR against appropriate upstream branch using $PREFERRED_REMOTE
- Include JIRA story link and test coverage summary in PR description
- **Final Check**: Verify PR was created successfully

## Functional Test Specific Guidelines

### Test Categories and Patterns

#### API Endpoint Testing
- Test all CRUD operations for relevant endpoints
- Validate request/response schemas
- Test authentication and authorization (PayPal-Application-Context)
- Verify error responses and status codes
- Test query parameters and filtering

#### Business Logic Testing
- Validate business rules and calculations
- Test data transformations and mappings
- Verify conditional logic and branching
- Test validation rules and constraints
- Ensure proper error handling
- For CPS: Test policy resolution, inheritance, and override logic

#### Configuration Testing (CPS-Specific)
- Test configuration hierarchy resolution
- Validate policy inheritance and overrides
- Test version compatibility
- Verify schema validation
- Test node composition and blueprint construction
- Validate reference mappings

#### Integration Testing
- Test database interactions and transactions
- Validate external service integrations
- Test configuration management (UCP integration)
- Verify caching behavior
- Test event publishing/consuming
- For CPS: Test SDK client integration patterns

#### Data Validation Testing
- Test input validation and sanitization
- Verify data format conversions
- Test boundary conditions and limits
- Validate required vs optional fields
- Test data consistency across operations
- For CPS: Test configuration JSON schema validation

### Mock Strategy Guidelines

#### When to Use Mocks (Isolated Tests)
- External service dependencies (payment processors, etc.)
- Database operations for pure logic testing
- Third-party APIs and integrations
- Time-dependent operations
- Complex setup scenarios
- For CPS: UCP interactions, external policy dependencies

#### When to Use Real Dependencies (Integrated Tests)
- Core service functionality end-to-end
- Database schema and query validation
- Configuration and environment setup
- Performance and load characteristics
- Contract validation with actual systems
- For CPS: Full configuration hierarchy resolution, policy inheritance testing

## Error Recovery Strategies

- **Service Name Detection Issues**: Ask user to provide service name manually
- **FunctionalTests Directory Not Found**: Guide user to locate or create test project structure
- **Test Compilation Issues**: Check classpath, dependencies, and imports
- **Test Execution Failures**: Analyze failure logs and provide specific guidance
- **Environment Issues**: Guide user through test environment setup
- **Data Setup Problems**: Help debug test data configuration issues
- **Mock Configuration Issues**: Assist with mock setup and verification
- **Configuration Validation Failures**: Guide through CPS config validator usage
- **Policy Resolution Issues**: Help debug hierarchy and inheritance problems

## Success Criteria

- [ ] Service name correctly detected from git repository
- [ ] FunctionalTests project located and accessible
- [ ] All new functional tests pass individually and in suite
- [ ] Both integrated and isolated test coverage implemented (as specified)
- [ ] Tests follow established CPS patterns and conventions
- [ ] Comprehensive coverage of JIRA story requirements
- [ ] Configuration validation tests included where applicable
- [ ] API contract tests for both products and product-variants endpoints
- [ ] Clear test documentation and maintainable code
- [ ] Proper test data setup and cleanup
- [ ] No conflicts with existing test suite
- [ ] PR created with comprehensive test coverage summary

## Testing Best Practices

### Test Organization
- **Clear Naming**: Test methods clearly describe what is being tested
- **Logical Grouping**: Related tests grouped in appropriate test classes
- **Proper Setup**: Use @Before/@BeforeEach for consistent test setup
- **Clean Teardown**: Use @After/@AfterEach for proper cleanup

### Test Quality
- **Single Responsibility**: Each test method tests one specific scenario
- **Independent Tests**: Tests should not depend on execution order
- **Meaningful Assertions**: Assertions provide clear failure messages
- **Complete Coverage**: Test happy path, edge cases, and error conditions

### Maintainability
- **DRY Principle**: Extract common test utilities and data setup
- **Clear Documentation**: Complex test scenarios include explanatory comments
- **Version Control**: Commit tests with clear, descriptive messages
- **Continuous Validation**: Ensure tests remain valid as code evolves