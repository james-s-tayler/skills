---
name: autoresearch
description: Use when the user wants to set up autonomous research/optimization on their repo — creates a GOAL.md fitness function and runs an improvement loop
user-invocable: true
argument-hint: "[optional context about what to optimize]"
---

# Autoresearch

You are setting up autonomous goal-driven optimization for the user's project. Follow these steps exactly in order.

## Step 1: Learn the GOAL.md pattern

Before doing anything else, study the GOAL.md specification:

1. Use the Bash tool to run `gh api repos/jmilinovich/goal-md/contents/template/GOAL.md -q .content | base64 -d` to read the canonical template
2. Read at least 2 examples by running the same command against these paths:
   - `examples/api-test-coverage.md`
   - `examples/perf-optimization.md`
   - `examples/docs-quality.md`
   - `examples/browser-grid.md`
3. Read the README with `gh api repos/jmilinovich/goal-md/contents/README.md -q .content | base64 -d`

Understand the five required elements: **Fitness Function**, **Improvement Loop**, **Action Catalog**, **Operating Mode**, and **Constraints**.

Do NOT proceed until you have read the template and at least 2 examples.

## Step 2: Understand the user's project

Scan the current repository to understand:
- What the project does (read README, main entry points, config files)
- What tools, languages, and frameworks are in use
- What testing, linting, or benchmarking infrastructure already exists
- What "better" could mean for this project

## Step 3: Gather the goal from the user

Use the AskUserQuestion tool to ask the following questions **one at a time, in order**. Wait for each answer before asking the next.

**Question 1:**
> What measurable metric would you like to optimize?

Provide options based on what you discovered about the project in Step 2 (e.g., test coverage, latency, build time, code quality score, etc.), plus an "Other" option for custom input. If the user passed $ARGUMENTS, use that context to inform your suggested options.

**Question 2:**
> Would you like to minimize it, maximize it, or set a target?

Options:
- **Minimize** — Drive the metric as low as possible (e.g., latency, error rate, bundle size)
- **Maximize** — Drive the metric as high as possible (e.g., coverage, throughput, score)
- **Set a target** — Converge on a specific value then stop

If they choose "Set a target", ask a follow-up to get the target value.

**Question 3:**
> What are the constraints I must obey?

Provide options based on common constraints for the project type (e.g., "Don't modify the public API", "Don't add new dependencies", "Tests must keep passing", "Don't change the database schema"), and allow multi-select plus custom input.

## Step 4: Write GOAL.md

Using the user's answers from Step 3 and what you learned about the project in Step 2, write a `GOAL.md` in the repository root following the template format exactly. It must include ALL of these sections:

- **Goal** — name and one-line description
- **Fitness Function** — a runnable `bash` command that outputs a score. Write a scoring script if one doesn't exist. The script MUST output JSON with at least `score` and `max` keys. Score must be an integer. Exit 0 even on bad scores.
- **Metric Definition** — formula and component table
- **Metric Mutability** — Locked, Split, or Open (choose based on what makes sense)
- **Operating Mode** — Converge, Continuous, or Supervised (derived from the user's minimize/maximize/target choice)
- **Stopping Conditions** — when to stop
- **Bootstrap** — exact commands to set up and get a baseline score
- **Improvement Loop** — the measure/diagnose/act/verify/commit-or-revert/log cycle
- **Action Catalog** — concrete actions organized by score component, with estimated point impact
- **Constraints** — the user's stated constraints, plus any obvious ones you inferred
- **File Map** — files the agent reads/writes with editability
- **Iteration Log** — `iterations.jsonl` format
- **When to Stop** — report template

Run the fitness function command to verify it works and record the baseline score in the Bootstrap section.

## Step 5: Surface unstated constraints

Use the AskUserQuestion tool to ask clarifying questions about constraints that might not have been stated but should be considered. Think about:

- Are there files or directories that should never be modified?
- Are there performance budgets or resource limits?
- Should the agent avoid certain approaches (e.g., no mocking in tests, no AI-generated content)?
- Are there style/formatting requirements?
- Should changes be backwards-compatible?
- Is there a max number of iterations or time budget?

Frame these as specific yes/no or multiple-choice questions relevant to what you know about the project. Do NOT ask generic questions — make them specific to the codebase you scanned.

If the user provides additional constraints, update the Constraints section of GOAL.md accordingly.

## Step 6: Start the autoresearch loop

Once GOAL.md is finalized:

1. Confirm the baseline score by running the fitness function
2. Tell the user: "GOAL.md is locked in. Starting the improvement loop. Beginning at score: [N]."
3. Begin executing the Improvement Loop defined in GOAL.md — measure, diagnose, pick the highest-impact action, make the change, verify, commit or revert, log to `iterations.jsonl`, and repeat
4. Follow the commit message format: `[S:NN->NN] component: what changed`
5. Continue until stopping conditions are met or the user interrupts
