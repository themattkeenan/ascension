---
name: performance-tuning
description: Use when performance is a concern - sluggish pages, slow queries, bloated bundles, high-latency APIs, or whenever someone says "optimize" or "make it faster"
---

# Performance Tuning

## Overview

Measure, pinpoint the bottleneck, fix that specific thing, measure again. Optimizing without a measurement is the failure this skill prevents: the bottleneck is almost never where intuition puts it, and effort spent anywhere else is wasted.

## When to Use

**Engage when:**
- Users report perceptible slowness
- Telemetry shows regression (response time, page load, throughput)
- Performance budgets are breached (bundle size, Core Web Vitals)
- Database queries exceed 100ms for routine operations
- API responses exceed 500ms for typical requests

**Leave it alone when:**
- "It might be slow someday" (measure when it actually is)
- "Best practice recommends optimizing X" (is X actually slow?)
- Current performance satisfies current requirements
- The feature does not yet work correctly (correctness first)

## The Entry Protocol

Before any optimization effort:

1. **Measure** the current performance. Done when you hold numbers, not hunches.
2. **Target** the required level. Done when a specific threshold is written down.
3. **Pinpoint** the bottleneck. Done when profiler data names the function, query or resource.
4. **Fix** that specific bottleneck, one change at a time.
5. **Verify** by re-running the identical measurement.

Done when the re-measurement meets the threshold, or the change has been reverted because it did not move the number.

## The Methodology

### Phase 1: Establish a Baseline

**You must have numbers before changing anything.**

| Dimension | How to Measure |
|---|---|
| Page load latency | Lighthouse, WebPageTest, browser DevTools Performance panel |
| API response time | Server logs, APM instrumentation, `time curl` |
| Query execution time | `EXPLAIN ANALYZE`, slow query log, ORM query logging |
| Bundle weight | `webpack-bundle-analyzer`, `source-map-explorer` |
| Memory consumption | Heap snapshots, `process.memoryUsage()` |
| CPU utilization | Flame charts via profiler, `perf`, `py-spy` |

Record the baseline. You need it to prove the optimization was effective.

### Phase 2: Locate the Bottleneck

**The bottleneck is almost never where you expect it.**

Profile, and take the function, query or resource consuming the most time as the optimization target. Everything else waits.

Check these locations in order (most common first):

1. **Database queries** -- N+1 patterns, absent indexes, full table scans
2. **Network calls** -- Sequential when parallelizable, no caching layer
3. **Serialization** -- Oversized payloads, unnecessary nested data
4. **Computation** -- Suboptimal algorithms, redundant processing
5. **I/O operations** -- File system access, disk reads, external API latency

### Phase 3: Resolve the Bottleneck

**Fix only what the profiler revealed. Change one variable at a time.**

#### Database Tuning

| Symptom | Remedy |
|---|---|
| N+1 queries | Eager loading / JOIN / batched query |
| Missing index | Add index on columns in WHERE/JOIN/ORDER BY clauses |
| Full table scan | Add appropriate index; constrain result set |
| Oversized result sets | Cursor-based pagination for large datasets |
| Expensive aggregations | Materialized views or pre-computed summaries |
| Lock contention | Tighten transaction scope; introduce read replicas |

```sql
-- BEFORE: Diagnose the problem
EXPLAIN ANALYZE SELECT * FROM transactions WHERE account_id = 789;

-- Look for: Seq Scan (missing index), high cost, slow execution
-- AFTER: Add index, re-run EXPLAIN ANALYZE, compare numbers
```

#### Frontend Tuning (Core Web Vitals)

| Metric | Threshold | Typical Remedies |
|---|---|---|
| LCP (Largest Contentful Paint) | < 2.5s | Optimize hero images, preload critical resources, enable SSR |
| INP (Interaction to Next Paint) | < 200ms | Break long tasks, defer non-critical JS, offload to web workers |
| CLS (Cumulative Layout Shift) | < 0.1 | Set explicit dimensions on media, avoid dynamic content insertion above fold |

**Bundle weight reduction:**
```
1. Audit: what occupies space in the bundle?
2. Remove unused dependencies
3. Code-split by route (lazy loading)
4. Ensure ESM imports for tree-shaking
5. Enable compression (gzip/brotli)
```

#### API Tuning

| Symptom | Remedy |
|---|---|
| Over-fetching | Return only requested fields; support sparse fieldsets |
| Under-fetching | Batch endpoints; return compound documents |
| No caching | Add Cache-Control headers and ETags |
| Synchronous heavy processing | Return 202 Accepted with async processing + polling |
| Oversized responses | Paginate, compress, or stream |
| Slow serialization | Profile the serializer; reduce nesting depth |

#### Algorithm Tuning

Only when the profiler points to computation as the bottleneck:

| From | To | When Applicable |
|---|---|---|
| O(n^2) nested loops | Hash map lookup O(n) | Large input sets |
| Repeated computation | Memoization or caching | Same inputs, expensive function |
| Synchronous blocking | Async / parallel execution | I/O-bound work |
| Full recomputation | Incremental update | Small mutations to large datasets |

### Phase 4: Confirm the Improvement

**Re-run the identical measurement. Compare the numbers.**

```
Baseline: API response 920ms
After optimization: API response 145ms
Required threshold: < 500ms -- ACHIEVED
```

If the improvement is not measurable, revert the change. An optimization that cannot be measured is not an optimization.

## Anti-Patterns to Avoid

| Anti-Pattern | Why It Fails | Better Approach |
|---|---|---|
| Premature caching | Adds complexity and stale-data risks | Optimize the query first |
| Premature indexing | Indexes degrade write throughput and consume storage | Add only when a query is demonstrably slow |
| Micro-optimizing tight loops | Saves nanoseconds, destroys readability | Profile first; only touch what the profiler flags |
| "Async all the things" | Adds cognitive complexity, harder debugging | Apply async only to I/O-bound operations |
| Optimizing in dev environment | Dev performance diverges from production | Profile in a production-like environment |
| Caching without invalidation | Stale data, consistency bugs | Design invalidation strategy before adding cache |

## Guardrails

- Have profiler output on hand before changing anything; when you are sure where the bottleneck is, profile anyway and let the profiler name the target.
- When an optimization suggests itself while you are in the code for something else, note it and finish the current task; it gets its own measurement later.
- A cache follows a measurement that names what is slow, and an attempt to optimize the query first.
- Code that runs once (startup routines, one-time migrations) stays as it is.
- Readability is traded only for a measured gain.
- Solve the scaling problem the numbers show now.
- One optimization at a time, re-measured between changes, so each change's effect is isolated.

## Related

- `ascension:system-design` -- architectural choices that influence performance characteristics
- `ascension:completion-gate` -- validate the optimization with measurements
- `ascension:quality-enforcement` -- performance budgets as automated quality gates
