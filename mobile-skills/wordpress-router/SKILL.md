---
name: wordpress-router
description: "识别 WordPress 项目类型（插件/主题/区块主题/完整站点），并路由到对应的领域工作流。触发词：WordPress路由、项目类型识别、WordPress分析、wp-triage。"
---

# WordPress Router

## When to use

Use this skill at the start of most WordPress tasks to:

- identify what kind of WordPress codebase this is (plugin vs theme vs block theme vs WP core checkout vs full site),
- pick the right workflow and guardrails,
- delegate to the most relevant domain skill(s).

## Inputs required

- Repo root (current working directory).
- The user’s intent (what they want changed) and any constraints (WP version targets, WP.com specifics, release requirements).

## Procedure

1. Run the project triage script:
2. Read the triage output and classify:
   - primary project kind(s),
   - tooling available (PHP/Composer, Node, @wordpress/scripts),
   - tests present (PHPUnit, Playwright, wp-env),
   - any version hints.
3. Route to domain workflows based on user intent + repo kind:
   - For the decision tree, read: `skills/wordpress-router/references/decision-tree.md`.
4. Apply guardrails before making changes:
   - Confirm any version constraints if unclear.
   - Prefer the repo’s existing tooling and conventions for builds/tests.

## Verification

- Re-run the triage script if you create or restructure significant files.
- Run the repo’s lint/test/build commands that the triage output recommends (if available).

## Failure modes / debugging

- If triage reports `kind: unknown`, inspect:
  - root `composer.json`, `package.json`, `style.css`, `block.json`, `theme.json`, `wp-content/`.
- If the repo is huge, consider narrowing scanning scope or adding ignore rules to the triage script.

## Escalation

- If routing is ambiguous, ask one question:
  - “Is this intended to be a WordPress plugin, a theme (classic/block), or a full site repo?”