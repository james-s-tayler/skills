# Claude Code Skills

A collection of Claude Code skills by [james-s-tayler](https://github.com/james-s-tayler).

## Installation

Add this marketplace to Claude Code:

```
/plugin marketplace add james-s-tayler/skills
```

Then install individual skills:

```
/plugin install <skill-name>@james-s-tayler-skills
```

## Available Skills

### autoresearch

Set up autonomous goal-driven optimization on any repo. Based on the [GOAL.md](https://github.com/jmilinovich/goal-md) pattern.

```
/plugin install autoresearch@james-s-tayler-skills
```

**What it does:**

1. Reads the GOAL.md spec and examples from [jmilinovich/goal-md](https://github.com/jmilinovich/goal-md)
2. Scans your project to understand what "better" means
3. Asks you what metric to optimize, the direction (minimize/maximize/target), and your constraints
4. Writes a complete GOAL.md with a runnable fitness function and scoring script
5. Asks clarifying questions about unstated constraints
6. Starts an autonomous improvement loop — measure, diagnose, act, verify, commit or revert, log, repeat

**Usage:**

```
/autoresearch
```
