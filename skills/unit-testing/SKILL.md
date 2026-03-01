---
name: ookami-unit-testing
description: >
  Comprehensive guide for writing high-quality unit tests across any tech stack.
  Use this skill whenever the user asks about: writing tests, what to test, how to structure tests,
  test naming conventions, mocking/stubbing strategies, test coverage concerns, TDD/BDD approaches,
  fixing brittle or slow tests, improving testability of existing code, or reviewing test quality.
  Also triggers for questions like "should I test this?", "how do I mock X?", "my test is too slow",
  "how do I test private methods?", "tests keep breaking when I refactor", or any code review involving tests.
  Always prefer this skill over general coding advice when tests are the primary concern.
---

# Unit Testing Skill

A framework-agnostic guide to writing meaningful, maintainable unit tests.

## 1. Framework Reference

Detect the stack from context and load the appropriate reference before generating test code:

| Stack | Reference |
|---|---|
| Ruby / Rails / RSpec | `references/rails-rspec.md` |
| Flutter / Dart | `references/flutter-dart.md` |
| Other | Apply the principles below directly |

---

## 2. Four Pillars of a Good Unit Test

Every test should be evaluated against these four pillars:

| Pillar | Question to Ask |
|---|---|
| **Regression protection** | Does this test catch bugs when code changes break existing behavior? |
| **Refactoring resistance** | Does this test stay green when internals change but behavior stays the same? |
| **Fast feedback** | Does this test run in milliseconds, not seconds? |
| **Maintainability** | Is this test easy to read, understand, and modify? |

The first two pillars are non-negotiable. A test that fails on refactoring (false positive) or misses regressions (false negative) is worse than no test.

> Code is not an asset — it is a liability. More code means more potential bugs. Tests earn their keep only when they protect against regressions while surviving refactors.

### What makes a good test suite

- Tests are integrated into the development cycle (unused tests have no value)
- Only the most important parts of the codebase are tested (domain model > glue code)
- Maximum value with minimum maintenance cost

---

## 3. Deciding What to Test

### Test

- Domain logic and business rules (the core of your application)
- Public interfaces and contracts
- Edge cases and boundary conditions (nil, empty, max values)
- Error paths and exception handling
- Behavior that has been buggy before

### Skip

- Implementation details (private methods, internal data structures)
- Framework or library internals
- Simple getters/setters with no logic
- Trivial glue code with no domain significance

> If your test breaks when you refactor *how* something works (but not *what* it does), the test is testing the wrong thing. This violates the refactoring resistance pillar.

---

## 4. Writing a Test

### Structure: Arrange -> Act -> Assert

```
# Arrange: set up the preconditions
user = build_user(role: :admin)

# Act: invoke the behavior under test
result = authorize(user)

# Assert: verify the outcome
expect(result).to eq(true)
```

- Keep each section small. If Arrange is huge, extract a helper.
- One behavior per test. If Assert has many conditions, split into multiple tests.

### Grouping: Reflect the Outcome Tree, Not the Code Structure

Test group hierarchy should mirror the **behavioral outcome tree** — the logical structure of what can happen from the caller's perspective. It should NOT mirror the code's internal branching (if/else, try/catch).

**Principle**: Sibling groups must be mutually exclusive outcomes at the same level of decision. If outcome B is a sub-case of outcome A, B belongs inside A, not beside it.

```
// BAD — sub-case is flat alongside its parent category
group('when download fails', () { ... });
group('when download fails with non-Exception error', () { ... }); // sub-case of "fails"
group('when download succeeds', () { ... });
```

```
// GOOD — sub-cases nested under their parent outcome
group('when download succeeds', () { ... });
group('when download fails', () {
  group('with Exception', () { ... });
  group('with non-Exception error', () { ... });
});
```

Why this matters:
- Flat siblings imply equal weight — readers assume three independent outcomes
- Nesting communicates that error variants are sub-cases of failure, not peers of success
- The tree structure serves as documentation of the behavior's decision points

**Important**: The outcome tree describes *observable behavior*, not implementation. Group by what the caller sees (success, failure, specific error type), not by internal code paths (`if _cache != null`, `when _pendingTask is null`).

```
// BAD — groups mirror internal implementation branching
group('when _pendingDownloadTask is null', () { ... });
group('when _pendingDownloadTask is not null', () { ... });

// GOOD — groups describe observable outcomes
group('when no download is in progress', () { ... });
group('when a download is already in progress', () { ... });
```

### Describe, Context, and Test Names

Tests have three semantic levels. Use distinct keywords for each:

| Concept | What it expresses | RSpec | Flutter/Dart | Jest/Vitest |
|---|---|---|---|---|
| **describe** | Test target (class, method, feature) | `describe` | `describe` (alias) | `describe` |
| **context** | Condition (`when ...`, `with ...`, `given ...`) | `context` | `context` (alias) | `describe` |
| **test** | Expected outcome — the assertion | `it` | `test` / `testWidgets` | `it` / `test` |

> **Flutter/Dart note:** Dart only provides `group`. Create `describe()` and `context()` aliases over `group()` in a shared test helper (e.g., `test/helpers/test_dsl.dart`) so the intent is explicit. See the Flutter reference for setup.

**Test names describe only the outcome** — never repeat the condition. Conditions belong in `context` blocks.

```dart
// BAD — condition flattened into the test name
group('FeaturedNewsCard', () {
  test('renders ShaderMask when useShader is true', () { ... });
  test('does not render ShaderMask when useShader is false', () { ... });
});

// GOOD — describe for target, context for condition, test for outcome
describe('FeaturedNewsCard', () {
  context('when useShader is true', () {
    test('renders ShaderMask', () { ... });
  });
  context('when useShader is false', () {
    test('does not render ShaderMask', () { ... });
  });
});
```

```ruby
# BAD — condition flattened into the test name
describe User do
  it 'returns nil when email is blank' do ... end
  it 'returns the user when email is valid' do ... end
end

# GOOD — describe for target, context for condition, it for outcome
describe User do
  context 'when email is blank' do
    it 'returns nil' do ... end
  end
  context 'when email is valid' do
    it 'returns the user' do ... end
  end
end
```

**Test name format:** **[action] [expected outcome]** — no condition suffix.

Conditions that are not worth a separate context (e.g., trivially obvious) can stay in the test name. Use judgment — the goal is readability, not rigid rules.

### Hardcode Expected Values — Never Replicate Production Logic

**This is critical.** Never reproduce the production algorithm in your test to compute the expected value. Hardcode the expected result directly.

```ruby
# BAD — domain knowledge leakage
# The test replicates the production algorithm (value1 + value2)
it 'adds two numbers' do
  value1 = 1
  value2 = 3
  expected = value1 + value2  # <-- leaking the algorithm!
  expect(Calculator.add(value1, value2)).to eq(expected)
end

# GOOD — hardcoded expected value
it 'adds two numbers' do
  expect(Calculator.add(1, 3)).to eq(4)
end
```

Why? Because deriving the expected value from inputs using the same logic as production code means you're just testing that the code matches itself. The test can never catch a bug in the algorithm. Hardcoding the expected value means the test verifies the result through a completely independent method — the human who wrote the test calculated the answer separately.

For multiple cases, use parameterized tests:

```ruby
# GOOD — parameterized with hardcoded expectations
[
  [1, 3, 4],
  [11, 33, 44],
  [100, 500, 600]
].each do |a, b, expected|
  it "adds #{a} and #{b} to get #{expected}" do
    expect(Calculator.add(a, b)).to eq(expected)
  end
end
```

---

## 5. Test Data

- Use factories/builders, not manual object creation or shared fixtures
- Create only the data the test needs — nothing more
- Prefer in-memory objects over persisted ones for speed

---

## 6. Test Helpers

Extract recurring test setup into shared helpers to reduce duplication and improve readability.

- **Test DSL**: If your framework lacks `describe`/`context` keywords, create aliases over the native grouping function. Place in a shared helper file.
- **Factories**: Create a base factory with a method like `generateFake()` that accepts named optional params. Each param defaults to a sensible value. Place in `test/factories/`.
- **Shared mocks/stubs**: For common dependencies (repositories, services, state managers), create reusable mock/stub classes. Place in `test/mocks/`.
- **Test builder**: Define a local helper in each test file that wraps the subject in the required environment (DI container, app shell, etc.) and accepts only the params that vary between tests.
- **Test config**: If the app has global state or singletons, create an init helper to set them up deterministically for tests.

> Keep helpers minimal. If a helper is only used in one file, inline it. Extract only when the same setup appears in 3+ files.

---

## 7. Mocking

**Mock at boundaries, not internals.**

### Mock

- External APIs and HTTP calls
- Email / notification / push services
- Time (`Time.now`, `DateTime.now`)
- File system access

### Don't Mock

- The class under test
- Simple value objects
- Pure functions

> Needing deep mocks to test a class signals too many responsibilities.

---

## 8. Test Isolation

- No shared mutable state between tests
- No dependency on test execution order
- Each test sets up and tears down its own world
- Same result every run, regardless of environment

---

## 9. TDD Workflow

**RED -> GREEN -> REFACTOR**

1. Write a failing test for the behavior you want
2. Write the minimum code to make it pass
3. Refactor with confidence — the test is your safety net
4. Repeat

Best for: new features, bug fixes (reproduce the bug first), complex algorithms.

> If `superpowers:test-driven-development` is installed, defer to that skill for strict TDD workflow enforcement.

---

## 10. Diagnostics

### When Tests Are Hard to Write

| Symptom | Likely Cause | Fix |
|---|---|---|
| Need to mock everything | Too many dependencies | Dependency injection |
| Can't test without the DB | Logic mixed with persistence | Extract service object |
| Private method needs testing | Logic in wrong place | Move to its own class |
| Test setup is huge | God object | Split responsibilities |

### Coverage

- Coverage measures lines, not confidence
- **High coverage + bad tests** = false confidence
- **Moderate coverage + meaningful tests** = real confidence
- Cover *behaviors*, not lines

---

## 11. Review Checklist

- [ ] `describe` is used for the test target, `context` for conditions — not raw `group` everywhere
- [ ] Conditions are expressed in `context` blocks, not flattened into test names
- [ ] Test name describes behavior, not implementation
- [ ] Each test is focused on one behavior
- [ ] Setup is minimal — only what this test needs
- [ ] Expected values are hardcoded, not computed from inputs
- [ ] Mocks are used sparingly and at boundaries
- [ ] Test survives a refactor of internals
- [ ] Failure message tells you exactly what broke
- [ ] Edge cases are covered (nil, empty, boundary)
- [ ] Both success and failure paths are tested
- [ ] Group hierarchy reflects the outcome tree — sub-cases are nested, not flat alongside parent categories

---

## 12. Anti-Patterns

| Anti-Pattern | Problem | Fix |
|---|---|---|
| Domain knowledge leakage | Test replicates production logic, can never catch algorithm bugs | Hardcode expected values |
| Testing everything in one test | One failure masks others | One behavior per test |
| Asserting on internal state | Breaks on refactor | Assert on outputs/side effects |
| `sleep` in tests | Slow and flaky | Mock time or use async helpers |
| Over-mocking | Tests pass but code is broken | Integration test at the boundary |
| Copy-paste setup | Maintenance burden | Shared helpers/factories |
| `it "should work"` | No information value | Describe the actual behavior |
| Testing trivial code | Maintenance cost > value | Focus on domain logic |
| Flat outcome groups | Sub-cases at same level as parent category obscure the decision tree | Nest sub-cases under their parent outcome |
| Condition in test name | Duplicates context across siblings, makes names long | Move condition to `context`/`group`, keep test name as outcome only |
