# Common Pitfalls

## "The agent said so"

Never a valid excuse. You reviewed and approved the output. You clicked merge. You are responsible for what goes into production.

The agent is a tool, not an authority. If it produces incorrect code and you approve it, the failure is in the review, not the generation.

**What to do instead:** Review every change critically. If something looks off, question it. Use `/code-review` and `/security-review` to get automated second opinions. But ultimately, the decision to merge is yours.

## "I don't understand the code but it works"

Do not merge what you do not understand. Passing tests are necessary but not sufficient.

If you cannot explain why the code works, you cannot maintain it, debug it, or extend it. You have introduced a black box into the codebase.

**What to do instead:** Ask the agent to explain the implementation. Ask it to walk through the logic step by step. If you still don't understand after the explanation, pair-program with a colleague. Understanding is not optional.

## Blind Acceptance

Reading the diff is not optional. Every line matters. Agent-generated code can contain:

- Correct-looking logic that handles the happy path but breaks on edge cases
- Unnecessary complexity (the agent doesn't always choose the simplest solution)
- Security issues that pass automated checks but are obvious to a human reviewer
- Style violations that hooks didn't catch

**What to do instead:** Read every line of the diff before approving. If the diff is too large to review effectively, the task was too large — break it down next time.

## Too-Large Tasks

The number one cause of poor agent output. When you give the agent a task that spans multiple files, multiple concerns, and multiple decisions, quality degrades.

Large tasks cause:
- Inconsistent patterns across files
- Lost context midway through implementation
- Diffs that are too large to review effectively
- Higher chance of subtle bugs

**What to do instead:** One view, one endpoint, one module at a time. If a feature requires changes in 15 files, break it into 5 tasks of 3 files each. Each task should be implementable, testable, and reviewable independently.

## Skipping Tests

"It's a small change." "I'll add tests later." "The agent got it right, I can tell."

AI generates code in minutes. There is no time pressure that justifies skipping tests. TDD is mandatory for AI-assisted workflows precisely because AI output needs verification.

**What to do instead:** Always use `/tdd` before `/tdd-implement`. No exceptions. The RED-GREEN-BLUE cycle exists for a reason.

## Over-Trust in Coverage Numbers

80% code coverage means 20% of the code is untested. That untested 20% might contain your most critical business logic, your error handling paths, or your security checks.

Coverage measures whether code was *executed* during testing. It does not measure whether the tests actually *verify* anything. A test that calls a function without asserting on the result achieves coverage without providing value.

**What to do instead:** Use mutation testing (Stryker) to validate that your tests catch actual bugs. A high mutation score means your tests are meaningful, not just present.

## Forgetting to Record Decisions

If you don't use `decide()` in Remindr/Fae, the next session starts from zero. You'll re-analyze problems you already solved, revisit decisions you already made, and potentially contradict previous choices.

**What to do instead:** Always record architecture decisions during the session where they're made. Use `decide()` for every significant choice: technology selections, pattern decisions, trade-off resolutions. The next session (or the next team member) will thank you.

## Not Checking Briefing

Starting a session without `briefing()` means you're working blind. You might:
- Duplicate work that was already completed
- Contradict decisions that were already recorded
- Implement something that was explicitly rejected in a previous session

**What to do instead:** Always start with `briefing()`. It takes seconds and gives you the full context of what has been decided, what's in progress, and what to watch out for.

## "While I'm Here" Improvements

In maintenance mode, you're fixing a bug. You notice the function could be cleaner. You refactor it. You notice the class has inconsistent naming. You rename things. The bug fix is now buried in a diff full of unrelated improvements.

Unrelated changes in a bug fix:
- Make the diff harder to review
- Increase the risk of introducing new bugs
- Make it harder to revert if something goes wrong
- Complicate the git history

**What to do instead:** Fix the bug and nothing more. Minimal diff = minimal risk. If you want to improve the code, create a separate task for that.

## Overriding Agent Models

Each agent has an optimal model assignment based on its task complexity:

| Agent              | Assigned Model | Why                                    |
|--------------------|----------------|----------------------------------------|
| tdd-test-writer    | Sonnet         | Fast, good at pattern-based generation |
| tdd-implementer    | Opus           | Needs deep reasoning for correctness   |
| code-reviewer      | Sonnet         | Pattern matching, fast turnaround      |
| security-reviewer  | Opus           | Security requires careful reasoning    |
| e2e-runner         | Haiku          | Execution and reporting, not analysis  |
| doc-updater        | Haiku          | Straightforward comparison task        |

Overriding a haiku agent to use opus wastes tokens without improving output — the task doesn't benefit from deeper reasoning. Overriding an opus agent to use haiku risks missing subtle issues that require careful analysis.

**What to do instead:** Trust the model assignments. They were chosen based on the cognitive demands of each task. If you think an assignment is wrong, discuss it with the team and update the template — don't override on a per-session basis.
