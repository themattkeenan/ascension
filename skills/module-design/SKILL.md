---
name: module-design
description: Shared vocabulary for designing deep modules. Use when designing or improving a module's interface, deciding where a seam goes, making code more testable, or when another skill needs the module/interface/depth/seam/adapter language.
---

# Module Design

Design **deep modules**: a lot of behaviour behind a small interface, placed at a clean seam, testable through that interface. Use this language wherever code is being designed or restructured. The payoff is **leverage** for callers, **locality** for maintainers, testability for everyone.

## Glossary

Use these terms exactly. Do not substitute "component", "service", "API", or "boundary" — consistent language is the point.

**Module** — anything with an interface and an implementation. Deliberately scale-agnostic: a function, a class, a package, a tier-spanning slice. *Avoid*: unit, component, service.

**Interface** — everything a caller must know to use the module correctly: the type signature, but also invariants, ordering constraints, error modes, required configuration, performance characteristics. *Avoid*: API, signature (both too narrow — they name only the type-level surface).

**Implementation** — what is inside the module.

**Depth** — leverage at the interface: how much behaviour a caller (or a test) can exercise per unit of interface they must learn. **Deep** = a lot of behaviour behind a small interface. **Shallow** = an interface nearly as complex as the implementation.

**Seam** *(Michael Feathers)* — a place where you can alter behaviour without editing in that place; the *location* at which a module's interface lives. Where the seam goes is its own decision, separate from what goes behind it. *Avoid*: boundary (overloaded with DDD's bounded context).

**Adapter** — a concrete thing that satisfies an interface at a seam. Names a *role*, not a substance.

**Leverage** — what callers get from depth: more capability per unit of interface learned.

**Locality** — what maintainers get from depth: change, bugs, and verification concentrate in one place. Fix once, fixed everywhere.

## Deep vs shallow

```
   DEEP (want)                SHALLOW (avoid)
┌──────────────────┐    ┌────────────────────────────┐
│  small interface │    │     large interface        │
├──────────────────┤    ├────────────────────────────┤
│                  │    │  thin implementation       │
│  deep            │    └────────────────────────────┘
│  implementation  │
└──────────────────┘
```

When designing an interface, ask: can I remove a method? Can I simplify these parameters? Can I hide more behind the seam?

## Principles

- **Depth is a property of the interface, not the implementation.** A deep module may be internally composed of small swappable parts; they just are not part of its interface. A module can have **internal seams** (private, used by its own tests) as well as the **external seam** at its interface.
- **The deletion test.** Imagine deleting the module. If complexity vanishes, it was a pass-through. If complexity reappears across N callers, it was earning its keep.
- **The interface is the test surface.** Callers and tests cross the same seam. Wanting to test *past* the interface means the module is the wrong shape.
- **One adapter is a hypothetical seam. Two adapters is a real one.** Introduce a seam only when something actually varies across it.

## Designing for testability

**Accept dependencies, do not create them.**

```typescript
// testable
function processOrder(order, paymentGateway) {}

// hard to test
function processOrder(order) {
  const gateway = new StripeGateway();
}
```

**Return results rather than producing side effects.**

```typescript
// testable
function calculateDiscount(cart): Discount {}

// hard to test
function applyDiscount(cart): void { cart.total -= discount; }
```

**Keep the surface small.** Fewer methods means fewer tests; fewer parameters means simpler setup.

## Rejected framings

- **Depth as a ratio of implementation lines to interface lines** (Ousterhout's original) rewards padding the implementation. Use depth-as-leverage.
- **"Interface" as the TypeScript keyword or a class's public methods** is too narrow: an interface here includes every fact a caller must know.
- **"Boundary"** is overloaded with DDD. Say **seam** or **interface**.

## Going deeper

- **Deepening a cluster given its dependencies** — [DEEPENING.md](DEEPENING.md): dependency categories, seam discipline, replace-don't-layer testing.
- **Exploring alternative interfaces** — [DESIGN-IT-TWICE.md](DESIGN-IT-TWICE.md): parallel subagents each designing the interface a radically different way, then compared on depth, locality, and seam placement.

`ascension:test-first` and `ascension:system-design` both speak this vocabulary; reach for this skill when the *words* are the problem, not the process.

---
*Adapted from [mattpocock/skills](https://github.com/mattpocock/skills) (MIT).*
