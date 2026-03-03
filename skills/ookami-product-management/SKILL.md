---
name: ookami-product-management
description: >
  ookami team product management guidelines covering thinking frameworks,
  development policy, issue writing, prioritization, and product development flow.
  Use this skill whenever creating issues, evaluating whether a feature should be
  built, prioritizing work, writing issue descriptions, structuring proposals,
  planning sprints, or discussing product direction. Also use when the user asks
  about how to write a good issue, what makes a feature worth building, how to
  structure WHY/WHAT in tickets, how to prioritize with MoSCoW, how to identify
  the right problem to solve, how to structure arguments with the pyramid principle,
  or how to involve customers in the development process.
---

# Product Management

Follow these guidelines when making product decisions, planning features, and writing issues.

**Important for LLM agents:** When helping a user create an issue or make a product decision, if any of the following are unclear, ask the user before proceeding:

- What **type** of issue is this? (Feature / Improvement / Bug / Tech Debt / Research)
- **Who** is the target user or customer?
- **Why now?** — What is the urgency or trigger?
- What **evidence** exists? (data, customer feedback, support tickets)
- What **MoSCoW priority** should this be?

Do not guess or fill in these fields with generic content. The value of the frameworks below depends on real, specific input from the issue creator.

## Thinking Frameworks

These frameworks form the foundation of how we think about problems, communicate solutions, and decide what to build.

### 1. Find the Right Problem (論点思考)

From "論点思考" by Kazunari Uchida:

> There are three levels of capability: being fast at tasks, being fast at solving problems, and being able to define the right problem. The last is the most valuable.

Before solving anything, ensure you are solving the **right thing**.

- **List candidate issues** — There are always multiple possible problems. Do not fixate on the first one.
- **Evaluate by impact** — Filter candidates by: Can it be solved? Is it actionable? Does solving it yield meaningful return?
- **Challenge the given problem** — The problem as stated by stakeholders is often not the real problem. Dig deeper. Ask "why" until you reach the true issue.
- **Increase your repertoire** — The more mental models and past experiences you draw from, the better your ability to spot the real issue.

### 2. Start with the Issue (イシューからはじめよ)

From "イシューからはじめよ" by Kazuto Ataka:

> Of all the things that seem like problems, only 2-3 out of 100 are truly worth solving right now.

An **issue** is not just a task or a bug. It is a question that, once answered, fundamentally changes the direction of action or decision-making.

- **Is this really worth solving right now?** — Not all problems deserve attention. Identify the few that truly matter.
- **Can we take a clear stance?** — A good issue has a hypothesis. "Should we do X?" is better than "What should we do?"
- **Does the answer change what we do?** — If the answer does not affect our actions, it is not a real issue.

### 3. Think in Hypotheses (仮説思考)

From "仮説思考" by Kazunari Uchida:

> Do not start by collecting information exhaustively. Start with a tentative answer and verify it.

Hypothesis-driven thinking is the opposite of exhaustive research. Form a conclusion early, then validate.

- **Start with a hypothesis, not data** — Even with incomplete information, construct a story: "We believe [X] because [Y]."
- **Use the hypothesis to focus** — Once you have a hypothesis, you only need to gather evidence that proves or disproves it. This eliminates wasted analysis.
- **Iterate rapidly** — If evidence disproves the hypothesis, revise it immediately. Do not cling to a wrong answer.
- **The faster you hypothesize, the faster you learn** — Speed of hypothesis formation determines speed of problem-solving. Embrace being wrong early.

### 4. Structure Communication (ピラミッド原則)

From "考える技術・書く技術" by Barbara Minto:

> Always present the conclusion first, then support it with grouped, logical arguments.

Use the Pyramid Principle to structure all written communication — issues, proposals, documents, and presentations.

#### SCQA: Frame the Narrative

Use SCQA to introduce any problem or proposal:

- **Situation** — The stable context everyone agrees on. ("We currently have X.")
- **Complication** — What changed or went wrong. ("But now Y is happening.")
- **Question** — The natural question that arises. ("So how do we address Y?")
- **Answer** — Your proposed solution or conclusion. ("We should do Z.")

#### MECE: Structure the Arguments

Organize supporting points so they are **Mutually Exclusive and Collectively Exhaustive** — no overlaps, no gaps.

- Group related ideas together under a single theme.
- Each group should be distinct from others (no overlap).
- Together, all groups should cover the full scope (no gaps).
- Aim for 3-5 supporting points per level.

#### Pyramid in Practice

```
Conclusion (Answer)
├── Supporting argument 1
│   ├── Evidence A
│   └── Evidence B
├── Supporting argument 2
│   ├── Evidence C
│   └── Evidence D
└── Supporting argument 3
    ├── Evidence E
    └── Evidence F
```

- **Top-down delivery**: State the conclusion first, then explain why.
- **Bottom-up construction**: When thinking through a problem, gather evidence first, then group and summarize upward.
- Apply this structure to issue descriptions, PR descriptions, proposals, and presentations.

### Applying the Frameworks Together

These frameworks work as a pipeline:

1. **論点思考** — Find the right problem to solve.
2. **イシューからはじめよ** — Confirm it is truly worth solving now.
3. **仮説思考** — Form a hypothesis and validate with minimum effort.
4. **ピラミッド原則** — Communicate your findings and proposals clearly.

## Customer-Facing Priorities (顧客視点の優先順位)

A service does not run by itself. The ookami team actively keeps it running every day. When deciding what to work on, follow this priority order — higher items always take precedence.

| Priority | What | Why |
|----------|------|-----|
| 1 | **24/7 Service Operation (サービス稼働)** | Nothing matters if the service is down. Incidents, monitoring, and on-call response come first. |
| 2 | **Foundation Development (基盤開発)** | Infrastructure, reliability, and platform work that keeps the service stable and scalable. |
| 3 | **New Feature Development (新機能開発)** | New capabilities for customers — only after operation and foundation are secure. |

- A service is not built in a day (サービスは一日にしてならず). Keeping it running is continuous, active work — not something that happens automatically.
- New features are important, but they are meaningless if the service is unstable or unavailable.
- When priorities conflict, always resolve in favor of the higher priority — operation over foundation, foundation over features.

### 伴走開発 (Customer-Accompanied Development)

New feature development (Priority 3) follows a 伴走開発 model — building alongside customers to solve real problems together.

Two dimensions of accompaniment drive this approach:

- **注力顧客との伴走** — Co-develop with focus customers. Build what they need, validate through direct partnership.
- **AIとの伴走** — Leverage AI as a development partner to accelerate delivery and quality.

## Development Policy (開発方針 4ヶ条)

These four principles evaluate **what** to build — primarily within foundation development and new feature development (Priorities 2–3). Every proposal must pass these filters.

### 1. Vision & Strategy (ビジョンと戦略)

Does it align with our vision? Does it fit the strategic scope (target customers and problems)?

- Build features that serve our vision and strategic direction.
- **Do not** build for audiences outside our core scope.

### 2. Universality (汎用性)

Does it benefit all customers, not just one?

- Prioritize solutions that serve the broader customer base.
- **Do not** build customizations that only benefit a specific customer.

### 3. Velocity of Change (変化量)

Innovation does not come from logic alone. Ship small changes frequently and at high speed.

- Prefer many small iterations over large releases.
- **Do not** pursue work that requires massive effort and stalls product velocity.
- Velocity must not compromise service stability — ship fast, but safely.

### 4. Continuity (継続性)

Does it contribute to customer retention? Will it still matter in 5 years?

- Build features that create long-term value for existing customers.
- **Do not** prioritize features that attract new customers but provide no value to existing ones.

## MoSCoW Prioritization

Use MoSCoW to classify every requirement or feature request. This ensures the team focuses on what truly matters within each delivery cycle.

### Must Have

Critical requirements. The release is a failure without these.

- Core functionality that the product cannot ship without.
- Regulatory, legal, or security requirements.
- Blockers that prevent users from completing essential workflows.
- Ask: "Will the product be usable without this?" — If no, it is a Must.

### Should Have

Important but not critical. High value, but the release can survive without them.

- Features that significantly improve the experience but have workarounds.
- Items that would cause dissatisfaction if missing, but do not block core usage.
- Ask: "Is there a workaround?" — If yes, it is likely a Should, not a Must.

### Could Have

Desirable. Nice-to-have improvements included only if time and resources permit.

- Polish, UX enhancements, minor quality-of-life improvements.
- Features with low development cost and moderate user benefit.
- Ask: "Would users notice if this were missing?" — If barely, it is a Could.

### Won't Have (this time)

Explicitly out of scope for this cycle. Agreed upon by stakeholders.

- Low-priority items that do not fit the current timebox.
- Ideas worth revisiting later but not now.
- Saying "Won't" is not rejection — it is focus. It prevents scope creep and protects Must/Should items.

### Applying MoSCoW

- Classify requirements **before** starting work, not during.
- Validate against the customer-facing priorities and the 4 development principles — a "Must" that violates Universality or Continuity should be challenged.
- When time runs short, drop Could items first, then Should items. Never drop Must items.

## Product Development Flow

### Define Purpose and Goals

Before starting any work, clearly define:

- **WHY** — The purpose. What customer problem are we solving? Why now?
- **WHAT** — The goal. What outcome do we expect? How will we measure success?

This enables:
- Better collaboration — the team (and customers) can contribute to finding the best solution.
- Meaningful retrospectives — you can evaluate whether the release achieved its goal.

### Involve Customers Throughout

Engage customers at every stage:

1. **Discuss** — Understand the problem with customers.
2. **Research** — Validate assumptions with real data and feedback.
3. **Create** — Build collaboratively, incorporating customer input.

### Retrospect After Release

After every release, review against the goals you set:

- Did it improve the customer experience?
- Are there further improvements to make?
- What new insights did we discover?

## Writing Good Issues

Every issue must clearly communicate **WHY** and **WHAT**. Use SCQA to frame and the Pyramid Principle to structure.

First, choose the issue type. Each type has a specific template — load the corresponding file when creating an issue.

### Issue Types

| Type | Label | When to use | Template |
|------|-------|-------------|----------|
| Feature | `feature` | New capability that does not exist yet | `templates/feature.md` |
| Improvement | `improvement` | Enhance existing functionality or UX | `templates/improvement.md` |
| Reliability | `reliability` | Performance, security, availability, scalability | `templates/reliability.md` |
| Dragon (Bug) | `dragon` | Something is broken or behaving unexpectedly | `templates/dragon.md` |
| Tech Debt | `tech-debt` | Internal code quality or maintainability | `templates/tech-debt.md` |
| Research | `research` | Need to investigate before committing | `templates/research.md` |

### Data Considerations

For Feature and Improvement issues, always consider:

- **Data volume** — Is the expected data count known?
  - If known: design for the specified count (e.g., 0, 5, 10 items).
  - If unknown: consider what happens at 10x or 100x scale (scrolling, pagination, load-more UX).
- **Backward compatibility** — Especially for native apps:
  - Once released, apps must be supported indefinitely.
  - Settings and configurations cannot be changed retroactively on installed apps.
  - Prepare for external dependency deprecation.
  - Web is more forgiving — changes can be applied universally.

### Issue Management Basics

1. All items in a Milestone must be tracked from TODO onward.
2. Priority is determined by position — higher in the board means higher priority.
3. Add estimates for forecasting and retrospectives.
4. The assignee is responsible for driving the issue to completion.
5. Confirm the purpose and target user story before starting work.
6. Ask for help early when blocked.
7. Write a summary and close when done.
