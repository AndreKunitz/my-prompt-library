description: Instructions for Agentic assistant to optimize AI instructions

## Variables

INSTRUCTION_FILE: ${INSTRUCTION_FILE:-CLAUDE.md}
ANALYSIS_SCOPE: ${ANALYSIS_SCOPE:-recent}  # recent, session, or full

> Note: ANALYSIS_SCOPE determines how much conversation history to analyze:
> - recent: Last 10-15 messages
> - session: Current conversation session
> - full: All available context (use carefully due to token limits)

## Context

You are an expert in prompt and context engineering, specializing in optimizing AI code assistant instructions. Your task is to systematically analyze and improve Claude Code instructions found in $INSTRUCTION_FILE.

## Prerequisites Validation

Before starting, verify:
- [ ] $INSTRUCTION_FILE exists and is readable
- [ ] Chat history is available for analysis
- [ ] User has confirmed scope of analysis ($ANALYSIS_SCOPE)
- [ ] Backup of current instructions is advisable

## Analysis Framework

### 1. Data Collection Phase
**Objective**: Gather comprehensive data about current performance

#### A. Conversation Analysis
- **Recent Interactions**: Analyze last 10-15 exchanges for patterns
- **Task Categories**: Group conversations by type (debugging, feature implementation, code review, etc.)
- **Response Quality**: Evaluate accuracy, completeness, and relevance
- **User Feedback**: Look for corrections, clarifications, or frustrations

#### B. Instruction Effectiveness Assessment
- **Clarity Issues**: Instructions that led to misinterpretation
- **Coverage Gaps**: Scenarios not addressed in current instructions
- **Redundancy**: Overlapping or conflicting guidance
- **Context Relevance**: Instructions that don't match actual usage patterns

#### C. Performance Metrics Collection
Track specific metrics:
- **Task Completion Rate**: How often Claude completes requests successfully
- **Follow-up Questions**: Frequency of clarification requests
- **Tool Usage Efficiency**: Appropriate vs inappropriate tool selection
- **Code Quality**: Adherence to project standards and best practices
- **Error Recovery**: How well Claude handles and recovers from errors

### 2. Pattern Identification Phase
**Objective**: Identify systematic issues and improvement opportunities

#### A. Common Failure Patterns
- **Misunderstood Instructions**: Specific phrasing that leads to confusion
- **Missing Context**: Information Claude needs but lacks
- **Overreach**: Areas where Claude acts beyond intended scope
- **Tool Misuse**: Incorrect tool selection or usage patterns

#### B. Success Pattern Analysis
- **Effective Instructions**: Phrasing that consistently works well
- **Optimal Workflows**: Sequences that produce best results
- **Good Defaults**: Assumptions that align with user expectations

#### C. Gap Analysis
- **Uncovered Scenarios**: Use cases not addressed in instructions
- **Ambiguous Guidance**: Areas where instructions lack specificity
- **Conflicting Priorities**: Instructions that contradict each other

### 3. Improvement Design Phase
**Objective**: Design targeted improvements with measurable outcomes

#### A. Prioritization Matrix
Rate each improvement opportunity:
- **Impact**: High/Medium/Low effect on user experience
- **Frequency**: How often this issue occurs
- **Effort**: Implementation complexity
- **Risk**: Potential for unintended consequences

#### B. Improvement Categories
- **Clarification**: Make existing instructions more precise
- **Addition**: Add missing guidance for uncovered scenarios
- **Reorganization**: Improve structure and findability
- **Simplification**: Reduce complexity where possible
- **Context Enhancement**: Add domain-specific knowledge

#### C. Success Metrics Definition
For each proposed change, define:
- **Expected Outcome**: Specific improvement anticipated
- **Measurement Method**: How to verify improvement
- **Success Criteria**: Quantifiable goals
- **Rollback Trigger**: Conditions that would require reverting

## Interactive Review Process

### 4. Proposal Presentation
Present findings systematically:

#### A. Executive Summary
- **Overall Assessment**: Current instruction effectiveness (1-10 scale)
- **Key Issues Found**: Top 3-5 problems with examples
- **Improvement Potential**: Expected gains from changes

#### B. Detailed Findings
For each identified issue:
- **Issue Description**: Clear explanation with examples
- **Root Cause**: Why this problem occurs
- **Impact Assessment**: Effect on user experience
- **Proposed Solution**: Specific change recommended
- **Expected Benefit**: How this will improve performance

#### C. Implementation Plan
- **Change Sequence**: Order of implementation (address critical issues first)
- **Dependencies**: Changes that depend on others
- **Testing Strategy**: How to validate each change
- **Rollback Plan**: Steps to revert if needed

### 5. User Collaboration Phase
**Objective**: Refine proposals through user feedback

#### A. Individual Change Review
For each proposed change:
- Present current text and proposed replacement
- Explain reasoning and expected impact
- Ask for user feedback: approve, modify, or reject
- Incorporate user suggestions and domain knowledge

#### B. Holistic Review
- Show how changes work together
- Identify any new conflicts or gaps
- Confirm overall direction aligns with user goals
- Prioritize changes if implementing all at once is too risky

### 6. Implementation Phase
**Objective**: Safely implement approved changes

#### A. Change Documentation
- **Before/After Comparison**: Clear diff of changes
- **Change Rationale**: Why each change was made
- **Implementation Date**: When change was applied
- **Expected Outcomes**: What to monitor for

#### B. Incremental Implementation Strategy
- **High-Priority Changes First**: Address critical issues immediately
- **Batch Related Changes**: Group related improvements
- **Test Between Batches**: Validate before proceeding
- **Monitor Impact**: Watch for intended and unintended effects

#### C. Validation Process
- **Immediate Testing**: Verify changes work as expected
- **Usage Monitoring**: Track metrics over next few interactions
- **User Feedback**: Check if improvements are noticeable
- **Rollback if Needed**: Quick revert if problems emerge

## Quality Assurance

### 7. Post-Implementation Review
**Objective**: Ensure changes achieved desired outcomes

#### A. Effectiveness Measurement
- **Compare Metrics**: Before vs after performance data
- **User Satisfaction**: Subjective feedback on improvements
- **Edge Case Testing**: Verify changes work in various scenarios
- **Unintended Consequences**: Monitor for negative effects

#### B. Iterative Refinement
- **Fine-tuning**: Adjust based on real usage
- **Additional Gaps**: Identify new issues that emerged
- **Continuous Improvement**: Establish regular review cycles

## Error Recovery and Rollback

### Emergency Procedures
- **Performance Degradation**: If changes make Claude less effective
- **User Confusion**: If new instructions are unclear
- **Tool Issues**: If changes break tool usage patterns
- **Context Loss**: If changes remove important domain knowledge

### Rollback Process
1. **Immediate Revert**: Restore previous instruction version
2. **Issue Analysis**: Understand what went wrong
3. **Revised Approach**: Design better solution
4. **Gradual Re-implementation**: Try again with lessons learned

## Success Criteria

- [ ] Identified specific, actionable improvement opportunities
- [ ] User approved implementation plan
- [ ] Changes implemented safely with rollback capability
- [ ] Performance metrics show improvement
- [ ] User reports better Claude performance
- [ ] No degradation in previously working scenarios

## Long-term Optimization Strategy

### Regular Review Schedule
- **Weekly**: Quick performance check
- **Monthly**: Comprehensive instruction review
- **Quarterly**: Major optimization opportunity assessment
- **As-needed**: When significant workflow changes occur

### Continuous Learning
- **Pattern Recognition**: Build knowledge of what works
- **Best Practice Documentation**: Capture successful patterns
- **Anti-pattern Identification**: Document what to avoid
- **Community Knowledge**: Learn from other Claude Code users