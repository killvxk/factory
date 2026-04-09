---
title: "Skills must self-review their output before presenting to user"
date: 2026-04-09
problem_type: process
components: [think, plan, check]
tags: [spec-review, self-review, quality-gate, document-review]
---

## Problem

/think and /plan produced output and immediately presented it to the user without reviewing it first. This meant design gaps, missing requirements, and inconsistencies only surfaced after the user pointed them out.

## Root Cause

Initial skill design focused on the exploration/creation phase but lacked an internal quality gate before the handoff to user. The "review" concept was only applied to code (/check), not to design specs or plans.

## Solution

Added mandatory spec review phases to both /think and /plan:

- **/think Spec Review** (4 dimensions): completeness, consistency, feasibility, clarity. Self-review for all depths; structured for Standard/Deep.
- **/plan Spec Review** (4 dimensions): completeness, ordering, isolation, verifiability. Self-review for all plans; structured for 6+ tasks; headless document-review dispatch for 10+ tasks.

These are **internal quality gates** (no user action needed), distinct from the 3 human gates (G1/G2/G3).

## Key Decision

Made spec review mandatory rather than optional. The original /plan had "optionally invoke factory:check in headless mode" which was too weak — nobody opts in to extra work. Mandatory gates with depth-calibrated intensity (lightweight for small, full for large) is the right balance.

## Prevention

Every skill that produces an artifact should have a self-review step before presenting. Apply the pattern: create → self-review → present.
