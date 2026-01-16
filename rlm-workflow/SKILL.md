---
name: rlm-workflow
description: Full RLM pipeline orchestration for complex problem solving. MUST BE USED when tackling problems that benefit from recursive decomposition, multi-perspective synthesis, and verification. Coordinates rlm-decompose, rlm-synthesize, rlm-verify, and rlm-distill into a coherent workflow.
allowed-tools: Read, Grep, Glob, Task, TodoWrite
---

# RLM Workflow

Full Recursive Language Model pipeline orchestration based on arxiv paper 2512.24601v1. Coordinates the complete decompose → distill → solve → synthesize → verify → iterate cycle for complex problem solving.

## When to Use

- Complex problems requiring multiple skills
- Tasks where quality is critical
- Problems that benefit from structured decomposition
- Situations where verification before delivery is essential
- Any task where "110% better than basic prompts" is worth the extra rigor

## Expected Outcomes

Based on the paper's findings:
- **28-33% improvement** on linear complexity tasks
- **23-58% improvement** on quadratic complexity tasks
- **66% cost reduction** through intelligent context filtering
- **100x context handling** through recursive decomposition

## The Full Pipeline

```
┌─────────────────────────────────────────────────────────────────┐
│                         RLM WORKFLOW                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   [1. Initialize] ──► [2. Distill] ──► [3. Decompose]          │
│                                              │                  │
│                                              ▼                  │
│                                    ┌─────────────────┐          │
│                                    │  [4. Solve]     │          │
│                                    │  Sub-problems   │          │
│                                    │  (can recurse)  │          │
│                                    └────────┬────────┘          │
│                                              │                  │
│                                              ▼                  │
│   [7. Iterate] ◄──── [6. Verify] ◄──── [5. Synthesize]         │
│        │                  │                                     │
│        │                  │                                     │
│        ▼                  ▼                                     │
│   [Re-decompose    [PASS] ──► [Complete]                       │
│    with feedback]                                               │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Phase Descriptions

### Phase 1: Initialize
Assess the problem and determine workflow parameters:
- **Complexity Assessment**: simple | moderate | complex
- **Workflow Depth**: shallow(1) | medium(2) | deep(3+)
- **Success Criteria**: How will we know when we're done?

### Phase 2: Distill
Apply `/rlm-distill` to extract relevant context:
- Filter out irrelevant information
- Focus on what matters for this problem
- Target 10-20% of original context

### Phase 3: Decompose
Apply `/rlm-decompose` to break into sub-problems:
- Identify independent chunks
- Map dependencies
- Plan recombination strategy

### Phase 4: Solve
Work each sub-problem:
- Can invoke the workflow recursively for complex sub-problems
- Track progress with TodoWrite
- Capture results for synthesis

### Phase 5: Synthesize
Apply `/rlm-synthesize` to combine results:
- Check consistency across sub-results
- Resolve contradictions
- Create unified output

### Phase 6: Verify
Apply `/rlm-verify` to validate:
- Three-tier verification cascade
- Syntactic → Semantic → Pragmatic
- Generate confidence score

### Phase 7: Iterate
If verification fails:
- Analyze what went wrong
- Return to Phase 3 with feedback
- Track iteration history

## Output Template

```markdown
## RLM Workflow Execution

### Phase 1: Initialize

**Problem Statement**: [Full problem]
**Complexity Assessment**: [simple|moderate|complex]
**Workflow Depth**: [shallow(1)|medium(2)|deep(3+)]
**Success Criteria**: [How we'll know we're done]

### Phase 2: Distill

**Distillation Summary**:
- Original scope: [files/tokens]
- Distilled scope: [files/tokens]
- Compression: [percentage]

**Key Context Extracted**:
[Most relevant information for this problem]

### Phase 3: Decompose

**Decomposition Strategy**: [by-domain|by-dependency|by-size]

| # | Sub-problem | Dependencies | Status |
|---|-------------|--------------|--------|
| 1 | [description] | none | [pending|done] |
| 2 | [description] | 1 | [pending|done] |
| 3 | [description] | 1, 2 | [pending|done] |

### Phase 4: Solve Sub-problems

#### Sub-problem 1: [title]
**Approach**: [how it was solved]
**Result**: [outcome]
**Confidence**: [0-100]%

#### Sub-problem 2: [title]
**Approach**: [how it was solved]
**Result**: [outcome]
**Confidence**: [0-100]%

[... repeat for all sub-problems ...]

### Phase 5: Synthesize

**Synthesis Strategy**: [sequential|parallel|hierarchical]

**Consistency Check**:
- Agreements: [what sub-results agree on]
- Contradictions: [resolved how]
- Gaps: [what's missing]

**Synthesized Output**:
[Combined result from all sub-problems]

### Phase 6: Verify

**Tier 1 (Syntactic)**: [PASS/FAIL]
**Tier 2 (Semantic)**: [PASS/FAIL]
**Tier 3 (Pragmatic)**: [PASS/FAIL]

**Overall Confidence**: [0-100]%
**Blocking Issues**: [list if any]

### Phase 7: Iteration Log

| Iteration | Verification Result | Feedback Applied |
|-----------|---------------------|------------------|
| 1 | [pass/fail] | [initial attempt] |
| 2 | [pass/fail] | [what was fixed] |

### Final Output

**Status**: [VERIFIED|IN_PROGRESS|BLOCKED]
**Confidence**: [0-100]%

[Final verified solution]

### Lessons Learned

- [What worked well]
- [What could be improved]
- [Patterns to remember]
```

## Workflow Depth Guidelines

### Shallow (Depth 1)
For moderately complex problems:
- Single decomposition pass
- Direct solve of sub-problems
- Standard verification

### Medium (Depth 2)
For complex problems:
- Decompose, then sub-decompose
- Nested synthesis
- Multiple verification passes

### Deep (Depth 3+)
For very complex problems:
- Recursive decomposition as needed
- Layered synthesis
- Iterative refinement until quality threshold

## Example: Implementing a Feature

```markdown
## RLM Workflow Execution

### Phase 1: Initialize

**Problem Statement**: Implement rate limiting middleware for all API endpoints
**Complexity Assessment**: moderate
**Workflow Depth**: medium (2)
**Success Criteria**:
- Rate limiter blocks requests exceeding threshold
- All endpoints protected
- Tests pass
- No performance regression >5ms

### Phase 2: Distill

**Distillation Summary**:
- Original scope: 2,847 Python files
- Distilled scope: 12 files (middleware, config, tests)
- Compression: 99.6%

**Key Context Extracted**:
- Existing middleware pattern in `middleware/base.py`
- Redis already available for distributed state
- Current avg response time: 45ms

### Phase 3: Decompose

**Decomposition Strategy**: by-domain

| # | Sub-problem | Dependencies | Status |
|---|-------------|--------------|--------|
| 1 | Design rate limiter interface | none | done |
| 2 | Implement Redis-backed counter | 1 | done |
| 3 | Create middleware class | 1, 2 | done |
| 4 | Add configuration options | 3 | done |
| 5 | Write unit tests | 1, 2, 3 | done |
| 6 | Integration testing | all | done |

### Phase 4: Solve Sub-problems

#### Sub-problem 1: Design rate limiter interface
**Approach**: Based on existing middleware patterns
**Result**: `RateLimiter` protocol with `is_allowed(key)` method
**Confidence**: 95%

#### Sub-problem 2: Implement Redis-backed counter
**Approach**: Sliding window algorithm with Redis sorted sets
**Result**: `RedisRateLimiter` class with atomic operations
**Confidence**: 90%

[... additional sub-problems ...]

### Phase 5: Synthesize

**Synthesis Strategy**: sequential (1→2→3→4→5→6)

**Consistency Check**:
- Agreements: Interface design used consistently across all components
- Contradictions: None
- Gaps: Load testing not yet performed

**Synthesized Output**:
Complete rate limiting middleware with Redis backend, configurable limits per endpoint, and comprehensive test coverage.

### Phase 6: Verify

**Tier 1 (Syntactic)**: PASS
- `ruff check .` - no issues
- `ty check` - no type errors

**Tier 2 (Semantic)**: PASS
- 24/24 unit tests passing
- 8/8 integration tests passing

**Tier 3 (Pragmatic)**: PASS
- Response time: 47ms (+2ms, within threshold)
- Code reviewed by team lead

**Overall Confidence**: 93%
**Blocking Issues**: None

### Phase 7: Iteration Log

| Iteration | Verification Result | Feedback Applied |
|-----------|---------------------|------------------|
| 1 | FAIL (Tier 2) | Fixed edge case in sliding window |
| 2 | PASS | N/A |

### Final Output

**Status**: VERIFIED
**Confidence**: 93%

Rate limiting middleware implemented in `middleware/rate_limit.py` with:
- Sliding window algorithm
- Redis-backed distributed counters
- Per-endpoint configuration
- Comprehensive test coverage

### Lessons Learned

- Sliding window algorithm more accurate than fixed window
- Redis sorted sets ideal for this use case
- Should add load testing to standard verification
```

## Integration with Reflexion Skills

After a successful workflow completion, consider:
- `/reflexion:memorize` - Persist lessons learned to CLAUDE.md
- `/reflexion:critique` - Get additional perspectives on the solution
- `/reflexion:reflect` - Reflect on the process for future improvements

## Tips for Effective Workflow Execution

1. **Start with Initialize** - Don't skip complexity assessment
2. **Distill aggressively** - Less context = better focus
3. **Decompose thoughtfully** - Good decomposition is half the battle
4. **Track everything** - Use TodoWrite for visibility
5. **Verify early and often** - Don't wait until the end
6. **Learn from iterations** - Each failure is information
7. **Document lessons** - Future you will thank present you
