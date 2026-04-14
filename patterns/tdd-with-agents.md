# TDD with Agents

## Why TDD Is Mandatory for AI Workflows

AI-generated code needs verification. The agent can produce syntactically correct, logically plausible code that is subtly wrong. Tests are how you catch that.

Without tests, you're trusting the output on faith. With TDD, you define the expected behavior **before** implementation, and the code must prove itself against those expectations.

There is no excuse to skip tests when AI can write and run them in minutes.

## The Red-Green-Blue Cycle

TDD with agents follows the classic Red-Green-Refactor cycle, but each phase is handled by a purpose-built agent with a specific model assignment.

### RED: Write Failing Tests

```
/tdd → spawns tdd-test-writer agent (sonnet)
```

The test writer agent analyzes the requirements and writes tests that **define** the expected behavior. These tests must fail — they describe functionality that does not yet exist.

What happens in this phase:
- Agent reads the task specification
- Agent examines existing code patterns and test conventions
- Agent writes test cases covering expected behavior, edge cases, and error conditions
- All tests fail (red) because no implementation exists yet

The test writer uses **sonnet** — fast enough for test generation, smart enough to cover edge cases.

### GREEN: Write Implementation

```
/tdd-implement → spawns tdd-implementer agent (opus)
```

The implementer agent reads the failing tests and writes **minimal code** to make them pass. Nothing more.

What happens in this phase:
- Agent reads the failing test suite
- Agent writes the simplest implementation that satisfies all tests
- All tests pass (green)

The implementer uses **opus** — the task requires understanding test intent and writing correct production code.

### BLUE: Refactor

With all tests green, refactor the implementation for clarity, performance, and adherence to project patterns. Tests must stay green throughout.

This phase may be manual or agent-assisted depending on the scope of cleanup needed.

## Key Principle: Separation of Concerns

Tests are written by a **different agent** than the implementation. This is deliberate.

If the same agent writes both tests and code, it can unconsciously write tests that match its own implementation assumptions rather than the actual requirements. Using separate agents creates genuine independence between specification and implementation.

| Phase | Agent             | Model  | Responsibility              |
|-------|-------------------|--------|-----------------------------|
| RED   | tdd-test-writer   | Sonnet | Define expected behavior    |
| GREEN | tdd-implementer   | Opus   | Write code to pass tests    |
| BLUE  | Developer/Agent   | —      | Refactor, keep tests green  |

## Success Criteria

Two metrics, both required:

1. **100% test pass rate** — Every test must pass. No skipped tests, no ignored failures.
2. **80%+ code coverage** — At least 80% of the implementation code is exercised by tests.

These are different things. You can have 100% pass rate with 10% coverage (you just didn't write enough tests). You can have 80% coverage with failing tests (the code runs but produces wrong results). Both numbers must hit their targets.

## The TDD Playbook

The file `.claude/docs/tdd-playbook.md` contains routing rules that determine which test framework and conventions to use based on file type:

| File Type       | Test Framework       | Convention              |
|-----------------|----------------------|-------------------------|
| C# / .NET       | xUnit + FluentAssertions | `*Tests.cs` files    |
| React / TypeScript | Vitest + React Testing Library | `*.test.tsx` files |
| API endpoints   | Integration tests    | `*.Integration.cs`      |
| Infrastructure  | Validation tests     | Per-module test files    |

The playbook ensures consistent test setup across the project without the developer needing to remember framework-specific details.

## Mutation Testing

Code coverage alone can be gamed. A test that calls a function but never asserts on the result achieves coverage without verifying correctness.

**Mutation testing** (using Stryker) validates that tests actually catch bugs:

1. Stryker makes small changes (mutations) to the production code — flipping conditionals, changing operators, removing statements.
2. It re-runs the test suite after each mutation.
3. If a mutation causes no test failure, the tests are insufficient.

A high mutation score means your tests genuinely verify behavior, not just execute code paths.

## Common Mistake: Skipping the RED Phase

The most frequent mistake is jumping straight to implementation:

```
BAD:  "Write login endpoint with tests"
      → Agent writes code AND tests together
      → Tests are shaped to pass the implementation, not to define requirements

GOOD: /tdd "Write login endpoint that accepts email/password and returns JWT"
      → tdd-test-writer defines expected behavior
      → Tests fail (no implementation yet)
      → /tdd-implement makes them pass
```

The test-first discipline catches spec misunderstandings early. If the tests don't match what you expected, you fix the tests before any implementation work begins. This is far cheaper than fixing both tests and code after the fact.
