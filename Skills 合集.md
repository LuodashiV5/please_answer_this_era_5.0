
This article discusses a systematic framework for choosing the right implementation approach in Claude Code projects, including purpose, decision framework, analysis workflow, output templates, common patterns, edge cases, examples, usage guidelines, and anti - patterns. Key points include:

1. **Purpose of the skill**: This skill helps determine the optimal technical architecture for Claude Code implementation by analyzing requirements, evaluating trade - offs, recommending approaches, and generating next - step instructions.
    
2. **Implementation options**: Skills are portable and token - efficient, suitable for reusable workflows; MCP is for external system connections but has high token consumption; Subagents offer context isolation and parallel execution, only available in Claude Code; Scripts have zero token consumption and are for well - defined tasks.
    
3. **Evaluation dimensions**: When analyzing requirements, consider token efficiency, development complexity, maintenance cost, execution speed, reusability, security/permissions, and cost control.
    
4. **Analysis workflow**: It includes understanding the requirement, identifying critical gaps, applying decision rules, recommending solutions, and generating next - step instructions.
    
5. **Common patterns**: For external API integration, recommend MCP + Skill; for repeatable workflows, Skill (possibly with Script); for complex multi - step analysis, Subagents + Skills; for simple automation, Script (wrapped in Skill if reusable).
    
6. **Edge cases**: When requirements are unclear, ask clarifying questions; when multiple valid approaches exist, recommend the primary one based on the most critical factor; when standard approaches don't fit, suggest a hybrid approach, a custom solution, or reconsider the requirement.
    
7. **Anti - patterns**: Avoid over - using MCP, creating Subagents for simple tasks, using Skills for non - reusable one - time tasks, ignoring token efficiency, and recommending Scripts for tasks needing AI reasoning..
## cc-advising-architecture

帮助在 Claude Code 中进行功能实现的选题，比如是直接做脚本好，还是做成 Skill 好，亦或是 SubAgents，助理做出更合理更科学的选型。

````Markdown

---
name: advising-architecture
description: Guides architectural decisions for Claude Code by analyzing requirements and recommending MCP, Skills, Subagents, or Scripts. Use when choosing implementation approaches.
---

# Advising Architecture

A systematic framework for choosing the right implementation approach in Claude Code projects.

## Purpose

This skill helps you determine the optimal technical architecture for your Claude Code implementation by:
- Analyzing your requirements through structured questions
- Evaluating trade-offs across multiple dimensions
- Recommending MCP, Skills, Subagents, Scripts, or hybrid approaches
- Generating actionable next-step instructions

## Core Decision Framework

### Implementation Options

**Skills** - Procedural knowledge packages
- Portable across conversations and projects
- Token-efficient via progressive disclosure
- Best for: Reusable workflows, domain expertise, multi-step processes

**MCP (Model Context Protocol)** - External system connections
- Real-time data access (APIs, databases, services)
- Standardized tool interfaces
- Best for: External integrations, live data, authenticated services
- Trade-off: High token consumption (definitions always loaded)

**Subagents** - Specialized isolated agents
- Independent context windows
- Tool permission control
- Parallel execution support
- Best for: Context isolation, concurrent tasks, permission constraints
- Note: Claude Code only

**Scripts** - Pure executable code
- Zero token consumption
- Deterministic execution
- Best for: Well-defined tasks, token-sensitive operations

### Evaluation Dimensions

When analyzing requirements, consider:
- **Token Efficiency**: Context window impact
- **Development Complexity**: Implementation effort
- **Maintenance Cost**: Long-term upkeep
- **Execution Speed**: Performance requirements
- **Reusability**: Cross-project/conversation needs
- **Security/Permissions**: Access control requirements
- **Cost Control**: API call expenses

## Analysis Workflow

### Step 1: Understand the Requirement

When a user describes their need, extract:
- Core functionality
- Input/output flow
- Integration points
- Usage context

### Step 2: Identify Critical Gaps

Determine which essential information is missing. Ask targeted questions about:

**Usage Pattern**
- One-time task, occasional use, or frequent operation?

**External Dependencies**
- Does this require external system access?
- Which specific systems/APIs?
- Are there existing SDKs or standard APIs?

**Process Characteristics**
- Is the workflow deterministic (predictable steps)?
- Does it require real-time data?

**Reusability Needs**
- Will this be used across multiple projects/conversations?
- Does it need to be shared?

**Execution Requirements**
- Does this need context isolation?
- Does this need parallel execution?
- Are there sensitive operations requiring permission control?

**Resource Constraints**
- Is token budget a concern?
- Are there API cost considerations?

### Step 3: Apply Decision Rules

Based on gathered information, evaluate:

```markdown
✓ Needs external API access → +MCP consideration
✓ Deterministic workflow → +Script consideration
✓ Cross-project reuse → +Skill consideration
✓ Context isolation needed → +Subagent consideration
✓ Token-sensitive → +Script over MCP
✓ Real-time data required → +MCP over Script
✓ Parallel execution → +Subagent
✓ Permission restrictions → +Subagent
```

### Step 4: Recommend Solution

Provide:
1. **Primary recommendation** with reasoning
2. **Optional complementary approaches** (e.g., "Skill + Script")
3. **Decision basis** showing key factors (✓/✗ format)
4. **Brief explanation** (1-2 sentences per point)

### Step 5: Generate Next-Step Instructions

Output instructions formatted for `/skill-creator` or other relevant tools.

## Output Template

When providing recommendations, use this structure:

```markdown
📋 Architecture Decision

Recommended Approach: [Primary] + [Optional supplement if applicable]

Decision Basis:
✓ [Factor that supports this choice] → [Implication]
✓ [Another supporting factor] → [Implication]
✗ [Factor that rules out alternatives] → [Why not that option]

Reasoning:
[1-2 sentence explanation of why this is the optimal choice]

[If hybrid approach]
Integration Pattern:
[How the components work together]

---

🎯 Next Steps - Instructions for /skill-creator

[Complete, copy-paste ready instructions including:
- Skill/component name
- Description
- Implementation approach
- Key functionality points
- Suggested tools/technologies]
```

## Common Patterns

### Pattern: External API Integration

**Indicators**: Mentions specific services (GitHub, Notion, Slack, databases)

**Typical recommendation**: MCP (connectivity) + Skill (usage expertise)

**Reasoning**: MCP handles authentication and API calls; Skill provides business logic and formatting

### Pattern: Repeatable Workflow

**Indicators**: "Every time I...", "Standard process for...", "Consistent format..."

**Typical recommendation**: Skill (potentially with Script)

**Reasoning**: Workflow knowledge should be portable; Script for token efficiency on deterministic parts

### Pattern: Complex Multi-Step Analysis

**Indicators**: "Research and analyze", "Gather from multiple sources", "Parallel investigation"

**Typical recommendation**: Subagents (specialized agents for each aspect) + Skills (shared methodology)

**Reasoning**: Context isolation prevents confusion; Skills ensure consistent approaches

### Pattern: Simple Automation

**Indicators**: "Convert X to Y", "Process these files", "Run this regularly"

**Typical recommendation**: Script (wrapped in Skill if reusable)

**Reasoning**: Deterministic tasks don't need AI reasoning; Skill wrapper enables reuse

## Edge Cases

### When Requirements Are Unclear

**Action**: Politely indicate which critical information is missing and ask specific clarifying questions. Do not guess or make assumptions about core requirements.

### When Multiple Valid Approaches Exist

**Action**: Recommend the primary approach based on the most critical factor, then briefly mention the alternative(s) with their trade-offs.

### When Standard Approaches Don't Fit

**Action**: Explain why the requirement is unusual, describe the limitation, and suggest either:
- A creative hybrid approach
- Building a custom solution (with considerations)
- Reconsidering the requirement

## Examples

### Example 1: Book Download Automation

**User**: "I want to download books from zlibrary and upload them to WeRead automatically"

**Analysis Process**:
- Core function: Download → Upload pipeline
- Missing info: Usage frequency, API availability, authentication

**Clarifying Questions**:
1. How often will you use this? (one-time, weekly, daily)
2. Do zlibrary and WeRead have official APIs or SDKs?
3. Is authentication required for these services?

**Typical Recommendation** (assuming frequent use, no standard APIs):
- Primary: Skill + Script
- Reasoning: Repeatable workflow (Skill for reusability) + deterministic steps (Script for efficiency)

### Example 2: Live Dashboard

**User**: "Create a dashboard showing GitHub PRs, CircleCI status, and Slack notifications"

**Analysis Process**:
- External systems: GitHub, CircleCI, Slack (all have APIs)
- Real-time data needed
- Ongoing use

**Recommendation**:
- Primary: MCP (for each service) + Skill (dashboard logic)
- Reasoning: Real-time API access requires MCP; Skill provides formatting and synthesis rules

### Example 3: Code Review Workflow

**User**: "Automatically review code changes for security issues"

**Analysis Process**:
- Sensitive operation (read-only needed)
- Repeatable methodology
- Need to prevent accidental modifications

**Recommendation**:
- Primary: Subagent + Skill
- Subagent: Limited to Read/Grep tools (safety)
- Skill: Security review checklist (shared expertise)

## Usage Guidelines

**When user provides vague description**:
- Acknowledge what you understand
- List 2-3 critical missing pieces
- Ask targeted questions (not a long list)

**When user asks "which should I use for X"**:
- Analyze X against the decision framework
- Provide recommendation with clear reasoning
- Include decision basis in ✓/✗ format

**When user has already started implementation**:
- Evaluate their current approach
- Provide constructive feedback
- Suggest refinements if needed (don't just validate)

## Anti-Patterns to Avoid

❌ Don't recommend MCP for everything just because it sounds sophisticated
❌ Don't create Subagents for simple tasks (over-engineering)
❌ Don't use Skills for one-time tasks that won't be reused
❌ Don't ignore token efficiency when it's clearly important
❌ Don't recommend Scripts for tasks that need AI reasoning

## Remember

- **Ask before assuming**: Get critical info first
- **Token efficiency matters**: It's often the deciding factor
- **Hybrid approaches are powerful**: Don't limit to single options
- **Context is king**: The same task might need different approaches in different contexts
- **Be opinionated but reasonable**: Provide clear recommendations with sound reasoning
````