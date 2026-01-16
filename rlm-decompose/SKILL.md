---
name: rlm-decompose
description: Recursive problem decomposition for complex tasks. MUST BE USED when facing multi-step problems, tasks exceeding context limits, or challenges requiring divide-and-conquer approaches. Breaks problems into sub-problems that can be independently solved.
allowed-tools: Read, Grep, Glob, Task
---

# RLM Decompose

Recursive problem decomposition based on the Recursive Language Models paper (arxiv 2512.24601v1), which demonstrates 28-33% improvement on linear complexity tasks through recursive decomposition.

## When to Use

- Complex multi-step problems
- Tasks that exceed comfortable context limits
- Problems requiring divide-and-conquer approaches
- Architecture decisions with multiple interacting components
- Refactoring large codebases
- Implementing features that span multiple systems

## Core Framework

### Step 1: Problem Analysis

Identify the problem's complexity class:

| Class | Description | Example | Decomposition Benefit |
|-------|-------------|---------|----------------------|
| Constant | Single-step, direct answer | "What's the return type?" | Minimal |
| Linear | N independent sub-tasks | "Update 10 API endpoints" | 28-33% improvement |
| Quadratic | N tasks with N interactions | "Refactor auth across services" | 23-58% improvement |

### Step 2: Decomposition Strategy

Choose the appropriate splitting approach:

**By Domain** - When problem spans multiple knowledge areas
- Frontend / Backend / Database
- Auth / Payments / Notifications
- UI / Business Logic / Data Access

**By Dependency** - When there's a clear execution order
- Parse → Validate → Transform → Store
- Analyze → Plan → Execute → Verify

**By Size** - When problem is too large for single pass
- First 1000 lines / Next 1000 lines
- Module A / Module B / Module C

### Step 3: Sub-problem Generation

For each sub-problem, ensure:
1. **Independence** - Can be solved without other results (or clearly state dependencies)
2. **Clarity** - Unambiguous scope and success criteria
3. **Solvability** - Within single-pass capability
4. **Testability** - Clear way to verify solution

### Step 4: Dependency Mapping

Create a directed graph of sub-problem dependencies:
```
[1] ──┐
      ├──► [3] ──┐
[2] ──┘          ├──► [5]
      ┌──► [4] ──┘
[1] ──┘
```

### Step 5: Recombination Plan

Define how sub-results merge:
- **Sequential** - Results feed forward (parsing pipeline)
- **Parallel** - Results combine at end (feature branches)
- **Hierarchical** - Nested combination (tree structure)

## Output Template

```markdown
## Decomposition Analysis

**Original Problem**: [Full problem statement]
**Complexity Class**: [constant|linear|quadratic]
**Decomposition Strategy**: [by-domain|by-dependency|by-size]
**Estimated Improvement**: [percentage based on complexity class]

### Sub-problems

| # | Sub-problem | Dependencies | Complexity | Success Criteria |
|---|-------------|--------------|------------|------------------|
| 1 | [description] | none | [low/med/high] | [how to verify] |
| 2 | [description] | 1 | [low/med/high] | [how to verify] |
| 3 | [description] | 1, 2 | [low/med/high] | [how to verify] |

### Dependency Graph

```
[1] ──► [2] ──► [3]
```

### Recombination Strategy

**Type**: [sequential|parallel|hierarchical]

**Merge Process**:
1. [How result 1 feeds into next step]
2. [How results combine]
3. [Final synthesis approach]

### Execution Order

1. Solve sub-problem 1 (no dependencies)
2. Solve sub-problem 2 (uses result from 1)
3. Solve sub-problem 3 (uses results from 1, 2)
4. Combine results using [strategy]
```

## Example: Decomposing a Feature Implementation

**Problem**: "Implement user authentication with OAuth2, session management, and role-based access control"

```markdown
## Decomposition Analysis

**Original Problem**: Implement user authentication with OAuth2, session management, and role-based access control
**Complexity Class**: quadratic (auth touches multiple systems)
**Decomposition Strategy**: by-domain
**Estimated Improvement**: 23-58%

### Sub-problems

| # | Sub-problem | Dependencies | Complexity | Success Criteria |
|---|-------------|--------------|------------|------------------|
| 1 | Design data models (User, Session, Role) | none | medium | Schema validates, relations correct |
| 2 | Implement OAuth2 provider integration | 1 | high | Can authenticate via Google/GitHub |
| 3 | Implement session management | 1 | medium | Sessions create, expire, invalidate |
| 4 | Implement RBAC middleware | 1, 3 | medium | Roles restrict endpoint access |
| 5 | Integration testing | 2, 3, 4 | medium | All flows work end-to-end |

### Dependency Graph

```
[1] ──┬──► [2] ──────────┐
      │                  │
      ├──► [3] ──► [4] ──┼──► [5]
      │           │      │
      └───────────┘      │
                         │
```

### Recombination Strategy

**Type**: hierarchical

**Merge Process**:
1. Data models (1) provide foundation for all auth components
2. OAuth2 (2) and Sessions (3) can develop in parallel
3. RBAC (4) requires sessions for user context
4. Integration tests (5) verify complete system
```

## Integration with Other RLM Skills

After decomposition:
- Use `/rlm-distill` to extract relevant context for each sub-problem
- Solve each sub-problem independently
- Use `/rlm-synthesize` to combine results
- Use `/rlm-verify` to validate the combined solution
- Use `/rlm-workflow` to orchestrate the full cycle

## Tips for Effective Decomposition

1. **Err toward more sub-problems** - 5 simple tasks beat 2 complex ones
2. **Make dependencies explicit** - Hidden dependencies cause failures
3. **Keep sub-problems focused** - Each should have a single responsibility
4. **Plan for failure** - Consider what happens if a sub-problem can't be solved
5. **Document assumptions** - Each sub-problem should state what it assumes about others
