---
title: "Claude Code marketplace.json source field cannot be '.'"
date: 2026-04-09
problem_type: bug
components: [marketplace, plugin-structure]
tags: [marketplace, plugin-json, source-field, claude-code, legacy-claude]
---

## Problem

After creating the Factory plugin, `/plugin marketplace add killvxk/factory` succeeded but the plugin failed to install with: `Invalid schema: plugins.0.source: Invalid input`.

## Root Cause

The `source` field in marketplace.json was set to `"."` (current directory). Claude Code's marketplace schema requires source to point to a subdirectory, not the root.

## Solution

Restructured the repo: moved all plugin content into a `factory/` subdirectory, changed `source` to `"./factory"`. Root level only contains marketplace.json and README.

Legacy context: this was the Claude Code marketplace fix that led to the original repo split. The Codex migration keeps the same idea but uses a repo-local plugin at `plugins/factory/` and `.agents/plugins/marketplace.json`.

```
.
├── .claude-plugin/marketplace.json  # source: "./factory"
├── README.md
└── factory/                         # actual plugin content
    ├── .claude-plugin/plugin.json
    ├── skills/
    ├── agents/
    └── ...
```

## Key Decision

Moved content into subdirectory rather than wrapping in an outer directory. This keeps the repo clean and follows the pattern used by killvxk-plugins marketplace (CodeMap, LongMemory all use `./SubDir/ccplugin` pattern).

## Prevention

Always check a working marketplace.json (e.g., killvxk-plugins) before writing a new one. Never use `"."` as source.
