---
name: rlm-synthesize
description: Multi-perspective synthesis for combining independent analyses. MUST BE USED when integrating results from decomposed sub-problems, merging multiple viewpoints, or creating unified outputs from parallel investigations.
allowed-tools: Read, Grep, Glob, TodoWrite
---

# RLM Synthesize

Multi-perspective synthesis based on the Recursive Language Models paper (arxiv 2512.24601v1). Combines independent analyses into coherent, unified outputs.

## When to Use

- Integrating results from decomposed sub-problems (after `/rlm-decompose`)
- Merging findings from multiple code explorations
- Combining multiple expert perspectives on a problem
- Creating unified documentation from multiple sources
- Resolving conflicting recommendations
- Building consensus from parallel investigations

## Core Framework

### Step 1: Input Inventory

Catalog all sub-results/perspectives being synthesized:
- Source identification (where did this come from?)
- Key findings (what's the main contribution?)
- Confidence level (how certain is this result?)
- Scope coverage (what part of the problem does it address?)

### Step 2: Consistency Check

Compare all inputs for:
- **Agreements** - Where multiple sources confirm the same conclusion
- **Contradictions** - Where sources disagree (explicit conflicts)
- **Gaps** - What aspects are not covered by any source
- **Overlaps** - Where multiple sources cover the same ground

### Step 3: Conflict Resolution

When sources contradict, apply resolution strategies:

| Strategy | When to Use | Example |
|----------|-------------|---------|
| Weighted Evidence | Sources have different reliability | Prefer test results over assumptions |
| Majority Voting | Multiple independent analyses | 3 of 4 approaches agree |
| Recency Bias | Information may be outdated | Newer API docs supersede old |
| Scope Precedence | Some sources are more specific | Module-specific beats generic |
| Expert Override | Clear authority exists | Security team decision is final |

### Step 4: Gap Analysis

Identify what's missing:
- Required elements not covered
- Edge cases not addressed
- Integration points not defined
- Error scenarios not handled

### Step 5: Unified Output

Create a coherent whole that:
- Preserves the best of each input
- Resolves all contradictions explicitly
- Acknowledges remaining gaps
- Provides a confidence score

## Output Template

```markdown
## Synthesis Report

### Input Sources

| # | Source | Key Finding | Confidence | Coverage |
|---|--------|-------------|------------|----------|
| 1 | [origin] | [main point] | [high/med/low] | [what it covers] |
| 2 | [origin] | [main point] | [high/med/low] | [what it covers] |
| 3 | [origin] | [main point] | [high/med/low] | [what it covers] |

### Consistency Analysis

**Agreements** (high confidence):
- [Finding confirmed by sources 1, 2, 3]
- [Finding confirmed by sources 1, 2]

**Contradictions**:
| Issue | Source A Says | Source B Says | Resolution |
|-------|---------------|---------------|------------|
| [topic] | [position] | [position] | [chosen + why] |

**Gaps Identified**:
- [ ] [Missing element 1] - Impact: [severity]
- [ ] [Missing element 2] - Impact: [severity]

### Synthesized Output

[Unified, coherent result that integrates all sources]

### Confidence Assessment

**Overall Confidence**: [0-100]%

| Aspect | Confidence | Rationale |
|--------|------------|-----------|
| Completeness | [%] | [why] |
| Correctness | [%] | [why] |
| Consistency | [%] | [why] |

### Recommendations

1. [Action based on synthesis]
2. [Follow-up to address gaps]
```

## Example: Synthesizing Code Analysis Results

**Context**: Three parallel investigations analyzed a performance issue

```markdown
## Synthesis Report

### Input Sources

| # | Source | Key Finding | Confidence | Coverage |
|---|--------|-------------|------------|----------|
| 1 | Database profiling | N+1 query in UserService | high | DB layer |
| 2 | APM traces | Slow response on /api/users | high | API layer |
| 3 | Code review | Missing pagination | medium | Business logic |

### Consistency Analysis

**Agreements** (high confidence):
- UserService is the bottleneck (confirmed by sources 1, 2)
- /api/users endpoint is affected (confirmed by sources 2, 3)

**Contradictions**:
| Issue | Source A Says | Source B Says | Resolution |
|-------|---------------|---------------|------------|
| Root cause | N+1 queries (DB) | Missing pagination (code) | Both contribute; N+1 is primary |

**Gaps Identified**:
- [ ] Cache layer not analyzed - Impact: medium (potential quick win)
- [ ] Load testing data missing - Impact: low (have production metrics)

### Synthesized Output

The performance issue in `/api/users` stems from two related problems:

1. **Primary**: N+1 query pattern in `UserService.findAll()` causing O(n) database calls
2. **Secondary**: Missing pagination allows unbounded result sets

**Recommended Fix Order**:
1. Add eager loading to eliminate N+1 (immediate 10x improvement)
2. Implement pagination with default limit of 50
3. Consider response caching for frequently accessed users

### Confidence Assessment

**Overall Confidence**: 85%

| Aspect | Confidence | Rationale |
|--------|------------|-----------|
| Completeness | 80% | Cache layer unexplored |
| Correctness | 95% | Multiple sources confirm |
| Consistency | 85% | Minor contradiction resolved |

### Recommendations

1. Fix N+1 query in UserService immediately
2. Add pagination before next release
3. Schedule cache layer analysis as follow-up
```

## Synthesis Patterns

### Pattern 1: Sequential Merge
When sub-problems form a pipeline:
```
[Result 1] → [Result 2] → [Result 3] → [Final]
```
Each result feeds into the next. Synthesize by verifying the chain.

### Pattern 2: Parallel Merge
When sub-problems are independent:
```
[Result 1] ─┐
[Result 2] ─┼─► [Final]
[Result 3] ─┘
```
Combine all results at once. Focus on consistency checks.

### Pattern 3: Hierarchical Merge
When some results group together:
```
[Result 1] ─┬─► [Group A] ─┐
[Result 2] ─┘              ├─► [Final]
[Result 3] ─┬─► [Group B] ─┘
[Result 4] ─┘
```
First synthesize groups, then synthesize groups into final.

## Integration with Other RLM Skills

- **After `/rlm-decompose`**: Synthesize the solutions to each sub-problem
- **Before `/rlm-verify`**: Always verify synthesized output before delivery
- **With `/rlm-distill`**: Use distill to extract relevant context for synthesis
- **In `/rlm-workflow`**: Synthesis is Phase 5 of the full pipeline

## Tips for Effective Synthesis

1. **Preserve provenance** - Always track where each piece came from
2. **Make conflicts explicit** - Don't silently resolve disagreements
3. **Quantify confidence** - Numerical confidence helps downstream decisions
4. **Address gaps honestly** - Unknown is better than wrong
5. **Keep synthesis reversible** - Someone should be able to trace back to sources
