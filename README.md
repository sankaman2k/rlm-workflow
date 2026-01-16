# RLM Workflow Skill

A practical guide to using the `/rlm-workflow` skill for complex problem solving.

---

## What is RLM Workflow?

RLM Workflow is an orchestration skill that coordinates four other skills into a structured problem-solving pipeline:

```
┌──────────┐    ┌─────────────┐    ┌───────────┐    ┌──────────────┐    ┌────────────┐
│ Distill  │ ─► │ Decompose   │ ─► │   Solve   │ ─► │  Synthesize  │ ─► │   Verify   │
│          │    │             │    │           │    │              │    │            │
│ Extract  │    │ Break into  │    │ Work each │    │ Combine      │    │ Validate   │
│ relevant │    │ sub-problems│    │ sub-task  │    │ results      │    │ solution   │
│ context  │    │             │    │           │    │              │    │            │
└──────────┘    └─────────────┘    └───────────┘    └──────────────┘    └────────────┘
                                                                               │
                                                                    ┌──────────┴──────────┐
                                                                    │                     │
                                                                  PASS                  FAIL
                                                                    │                     │
                                                                    ▼                     ▼
                                                                Complete            Iterate with
                                                                                    feedback
```

---

## When to Use It

**Use `/rlm-workflow` when:**

| Scenario | Example |
|----------|---------|
| Multi-step implementation | "Add OAuth2 with session management and RBAC" |
| Large refactoring | "Migrate from REST to GraphQL" |
| Complex debugging | "Find and fix all memory leaks" |
| Architecture decisions | "Design a caching strategy for our API" |
| Quality-critical work | "Implement payment processing" |

**Don't use it when:**

| Scenario | Better Alternative |
|----------|-------------------|
| Simple bug fix | Direct fix |
| Single file change | Direct edit |
| Quick question | Direct answer |
| Code review | `/reflexion:critique` |

---

## How to Invoke

Simply type:

```
/rlm-workflow
```

Then describe your problem. The skill will guide you through each phase.

---

## The Seven Phases

### Phase 1: Initialize

**What happens:** Assess problem complexity and set workflow parameters.

**You provide:**
- Full problem statement
- Any constraints or requirements
- Success criteria (how you'll know it's done)

**Output:**
```markdown
**Problem Statement**: [your problem]
**Complexity Assessment**: moderate
**Workflow Depth**: medium (2)
**Success Criteria**:
- Tests pass
- Performance within bounds
- Code reviewed
```

---

### Phase 2: Distill

**What happens:** Extract only the relevant context from your codebase.

**Why it matters:** The paper shows 91% accuracy at 66% cost through intelligent filtering. Less irrelevant context = better focus.

**Output:**
```markdown
**Distillation Summary**:
- Original scope: 2,847 files
- Distilled scope: 12 files
- Compression: 99.6%

**Key Context Extracted**:
- Existing auth middleware pattern
- Database schema for users
- Current test patterns
```

---

### Phase 3: Decompose

**What happens:** Break the problem into independent, solvable sub-problems.

**Output:**
```markdown
| # | Sub-problem | Dependencies | Status |
|---|-------------|--------------|--------|
| 1 | Design data models | none | pending |
| 2 | Implement core logic | 1 | pending |
| 3 | Add API endpoints | 1, 2 | pending |
| 4 | Write tests | 1, 2, 3 | pending |
```

---

### Phase 4: Solve

**What happens:** Work through each sub-problem systematically.

For complex sub-problems, the workflow can recurse (decompose the sub-problem further).

**Output:**
```markdown
#### Sub-problem 1: Design data models
**Approach**: Based on existing patterns in models/
**Result**: User, Session, Role models created
**Confidence**: 95%
```

---

### Phase 5: Synthesize

**What happens:** Combine all sub-problem results into a unified solution.

**Output:**
```markdown
**Consistency Check**:
- Agreements: All components use same auth interface
- Contradictions: None found
- Gaps: Load testing not included

**Synthesized Output**:
Complete authentication system with OAuth2, sessions, and RBAC
```

---

### Phase 6: Verify

**What happens:** Three-tier validation cascade.

| Tier | Question | Checks |
|------|----------|--------|
| 1 - Syntactic | Is it correct? | Compiles, lints, formats |
| 2 - Semantic | Does it work? | Tests pass, logic correct |
| 3 - Pragmatic | Is it good? | Performance, maintainability |

**Output:**
```markdown
**Tier 1 (Syntactic)**: PASS
**Tier 2 (Semantic)**: PASS
**Tier 3 (Pragmatic)**: PASS

**Overall Confidence**: 93%
```

---

### Phase 7: Iterate (if needed)

**What happens:** If verification fails, loop back with feedback.

**Output:**
```markdown
| Iteration | Result | Feedback Applied |
|-----------|--------|------------------|
| 1 | FAIL (Tier 2) | Fixed race condition in session handler |
| 2 | PASS | N/A |
```

---

## Complete Example: Adding a Feature

### The Request

```
/rlm-workflow

Add rate limiting to our API:
- Per-user limits (100 req/min)
- Per-endpoint limits (configurable)
- Redis-backed for distributed deployments
- Should not add >10ms latency
```

### The Execution

```markdown
## RLM Workflow Execution

### Phase 1: Initialize

**Problem Statement**: Add rate limiting to API with per-user and per-endpoint limits, Redis-backed, <10ms latency overhead
**Complexity Assessment**: moderate
**Workflow Depth**: medium (2)
**Success Criteria**:
- Rate limiter blocks requests exceeding threshold
- Configurable per-endpoint limits
- Works in distributed deployment (Redis)
- Latency overhead <10ms
- All tests pass

### Phase 2: Distill

**Distillation Summary**:
- Original scope: 847 Python files
- Distilled scope: 8 files
- Compression: 99%

**Key Context Extracted**:
- Middleware pattern: `middleware/base.py` uses `BaseMiddleware` class
- Redis client: Already configured in `core/redis.py`
- Config pattern: YAML files in `config/`
- Current latency: 45ms average

### Phase 3: Decompose

**Decomposition Strategy**: by-domain

| # | Sub-problem | Dependencies | Status |
|---|-------------|--------------|--------|
| 1 | Design rate limiter interface | none | done |
| 2 | Implement sliding window algorithm | none | done |
| 3 | Create Redis storage backend | 2 | done |
| 4 | Build middleware class | 1, 3 | done |
| 5 | Add YAML configuration | 4 | done |
| 6 | Write unit tests | 1-5 | done |
| 7 | Write integration tests | 1-6 | done |

### Phase 4: Solve Sub-problems

#### Sub-problem 1: Design rate limiter interface
**Approach**: Protocol class matching existing middleware patterns
**Result**:
```python
class RateLimiter(Protocol):
    async def is_allowed(self, key: str) -> bool: ...
    async def get_remaining(self, key: str) -> int: ...
```
**Confidence**: 95%

#### Sub-problem 2: Implement sliding window algorithm
**Approach**: Sliding window counter (more accurate than fixed window)
**Result**: Algorithm implemented with O(1) check complexity
**Confidence**: 90%

#### Sub-problem 3: Create Redis storage backend
**Approach**: Redis sorted sets for sliding window
**Result**: `RedisRateLimiter` class with atomic Lua scripts
**Confidence**: 92%

#### Sub-problem 4: Build middleware class
**Approach**: Extend `BaseMiddleware`, inject rate limiter
**Result**: `RateLimitMiddleware` with user/endpoint key generation
**Confidence**: 95%

#### Sub-problem 5: Add YAML configuration
**Approach**: New `config/rate_limits.yaml` file
**Result**: Per-endpoint config with defaults
**Confidence**: 98%

#### Sub-problem 6: Write unit tests
**Approach**: Pytest with Redis mocking
**Result**: 18 unit tests covering all paths
**Confidence**: 95%

#### Sub-problem 7: Write integration tests
**Approach**: Test with real Redis in Docker
**Result**: 6 integration tests
**Confidence**: 90%

### Phase 5: Synthesize

**Synthesis Strategy**: sequential (1→2→3→4→5→6→7)

**Consistency Check**:
- Agreements: All components use same `RateLimiter` protocol
- Contradictions: None
- Gaps: Load testing would be nice (not required)

**Synthesized Output**:
Complete rate limiting system:
- `middleware/rate_limit.py` - Main middleware
- `services/rate_limiter.py` - Redis-backed limiter
- `config/rate_limits.yaml` - Configuration
- 24 tests with 94% coverage

### Phase 6: Verify

**Tier 1 (Syntactic)**: PASS
- `ruff check .` - 0 issues
- `ty check` - 0 errors
- All imports resolve

**Tier 2 (Semantic)**: PASS
- 18/18 unit tests passing
- 6/6 integration tests passing
- Edge cases handled (burst, recovery, distributed)

**Tier 3 (Pragmatic)**: PASS
- Latency: 47ms (+2ms) ✓ under 10ms threshold
- Code follows existing patterns
- Config is intuitive

**Overall Confidence**: 93%
**Blocking Issues**: None

### Phase 7: Iteration Log

| Iteration | Result | Feedback Applied |
|-----------|--------|------------------|
| 1 | PASS | N/A (first attempt succeeded) |

### Final Output

**Status**: VERIFIED
**Confidence**: 93%

Files created/modified:
- `middleware/rate_limit.py` (new)
- `services/rate_limiter.py` (new)
- `config/rate_limits.yaml` (new)
- `tests/unit/test_rate_limiter.py` (new)
- `tests/integration/test_rate_limit_middleware.py` (new)
- `middleware/__init__.py` (modified - export)
- `config/settings.py` (modified - load rate limits)

### Lessons Learned

- Sliding window more accurate than fixed window for rate limiting
- Redis sorted sets are perfect for this use case
- Lua scripts essential for atomic operations
- Should consider adding metrics/observability in future
```

---

## Example: Debugging a Complex Issue

### The Request

```
/rlm-workflow

Users report intermittent 500 errors on checkout.
Happens ~5% of the time, no clear pattern.
Need to find root cause and fix.
```

### The Execution

```markdown
## RLM Workflow Execution

### Phase 1: Initialize

**Problem Statement**: Intermittent 500 errors on checkout (~5% of requests)
**Complexity Assessment**: complex (debugging with unclear cause)
**Workflow Depth**: deep (3) - may need multiple investigation rounds
**Success Criteria**:
- Root cause identified
- Fix implemented
- Error rate drops to <0.1%
- No regression in checkout flow

### Phase 2: Distill

**Distillation Summary**:
- Original scope: Full codebase + 3 days of logs
- Distilled scope: checkout module + error logs + related services
- Compression: 95%

**Key Context Extracted**:
- Checkout flow: Cart → Payment → Inventory → Confirmation
- Error logs show: "Connection refused" to inventory service
- Pattern: Errors cluster around XX:00 and XX:30 (cron times?)
- Inventory service has 2 replicas behind load balancer

### Phase 3: Decompose

**Decomposition Strategy**: by-dependency (follow the error path)

| # | Sub-problem | Dependencies | Status |
|---|-------------|--------------|--------|
| 1 | Analyze error logs for patterns | none | done |
| 2 | Review inventory service health | 1 | done |
| 3 | Check connection pool settings | 2 | done |
| 4 | Examine cron job interference | 1 | done |
| 5 | Identify root cause | 1-4 | done |
| 6 | Implement fix | 5 | done |
| 7 | Verify fix in staging | 6 | done |

### Phase 4: Solve Sub-problems

#### Sub-problem 1: Analyze error logs
**Result**: Errors correlate with inventory sync cron at XX:00, XX:30

#### Sub-problem 2: Review inventory service health
**Result**: Service healthy, but CPU spikes to 100% during sync

#### Sub-problem 3: Check connection pool settings
**Result**: Pool size = 10, timeout = 5s, no retry logic

#### Sub-problem 4: Examine cron job interference
**Result**: Inventory sync locks database tables for 3-8 seconds

#### Sub-problem 5: Identify root cause
**Result**:
- Cron job locks tables
- Checkout queries timeout waiting for lock
- Connection pool exhausted
- New requests get "connection refused"

#### Sub-problem 6: Implement fix
**Result**:
- Add retry logic with exponential backoff
- Increase connection pool to 25
- Add circuit breaker for inventory service
- Optimize cron job to use smaller batches

#### Sub-problem 7: Verify fix in staging
**Result**: Simulated load during cron - 0 errors

### Phase 5: Synthesize

**Root Cause**: Inventory sync cron job causes database lock contention,
exhausting connection pool during high-traffic checkout periods.

**Fix**: Multi-layered resilience
1. Retry logic catches transient failures
2. Larger pool handles burst
3. Circuit breaker prevents cascade
4. Optimized cron reduces lock duration

### Phase 6: Verify

**Tier 1**: PASS - Code compiles, tests pass
**Tier 2**: PASS - Unit tests for retry logic, circuit breaker
**Tier 3**: PASS - Staging load test during cron = 0 errors

**Overall Confidence**: 88%

### Final Output

**Status**: VERIFIED
**Root Cause**: Database lock contention from inventory sync cron
**Fix**: Retry logic + larger pool + circuit breaker + optimized cron
**Expected Result**: Error rate <0.1%
```

---

## Tips for Best Results

### 1. Be Specific in Your Request
```
# Good
"Add user authentication with JWT tokens, refresh token rotation,
and secure httpOnly cookies. Must work with existing User model."

# Vague
"Add auth to the app"
```

### 2. Define Success Criteria
```
# Good
"Success means: tests pass, latency <100ms, no security warnings from OWASP ZAP"

# Vague
"Should work well"
```

### 3. Provide Constraints
```
# Good
"Must use existing Redis instance, cannot add new dependencies,
needs to be backwards compatible with v2 API"

# Missing context
"Add caching"
```

### 4. Trust the Process
- Let each phase complete before moving on
- Don't skip verification
- If iteration is needed, that's the system working correctly

### 5. Learn from Iterations
- Failed verifications provide valuable feedback
- Document lessons learned for future reference
- Consider using `/reflexion:memorize` after completion

---

## Integration with Other Skills

| After RLM Workflow | Use This | When |
|--------------------|----------|------|
| Successful completion | `/reflexion:memorize` | To persist lessons learned |
| Want additional review | `/reflexion:critique` | For multi-perspective critique |
| Need to improve solution | `/reflexion:reflect` | For iterative refinement |

Available from: https://cek.neolab.finance/plugins/reflexion
---

## Quick Reference

```
/rlm-workflow

[Describe your complex problem here]

Include:
- What you're trying to achieve
- Any constraints or requirements
- How you'll know it's successful
```

## Attribution                                                                                                                                           
                                                                                                                                                           
  The RLM skills suite is inspired by the Recursive Language Models paper:                                                                                 
                                                                                                                                                           
  > **Recursive Language Models**                                                                                                                          
  > Alex L. Zhang, Tim Kraska, Omar Khattab                                                                                                                
  > arXiv:2512.24601, 2025                                                                                                                                 
  > https://arxiv.org/abs/2512.24601                                                                                                                       
                                                                                                                                                           
  The original RLM implementation is available at [github.com/alexzhang13/rlm](https://github.com/alexzhang13/rlm).                                        
                                                                                                                                                           
  BibTeX (for formal citation)                                                                                                                             
```
@misc{zhang2025recursivelanguagemodels,
      title={Recursive Language Models}, 
      author={Alex L. Zhang and Tim Kraska and Omar Khattab},
      year={2025},
      eprint={2512.24601},
      archivePrefix={arXiv},
      primaryClass={cs.AI},
      url={https://arxiv.org/abs/2512.24601}, 
}
```
The skill handles the rest: distill → decompose → solve → synthesize → verify → iterate.
