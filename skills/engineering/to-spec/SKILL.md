---
name: to-spec
description: "Turn the current conversation into a spec and publish it to the project issue tracker: no interview, just synthesis of what you've already discussed."
disable-model-invocation: true
---

This skill takes the current conversation context and codebase understanding and produces a spec. Do NOT interview the user; just synthesize what you already know.

The issue tracker and triage label vocabulary should have been provided to you. If not, tell the user to run `/setup-matt-pocock-skills`.

## Process

1. Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain glossary vocabulary throughout the spec, and respect any ADRs in the area you're touching.

2. Sketch out the seams at which you're going to test the feature. Existing seams should be preferred to new ones. Use the highest seam possible. If new seams are needed, propose them at the highest point you can. The fewer seams across the codebase, the better - the ideal number is one.

Check with the user that these seams match their expectations.

3. Write the spec using the template below, then publish it to the project issue tracker. Apply the `ready-for-agent` triage label - no need for additional triage.

<spec-template>

## Problem Statement

The problem that the user is facing, from the user's perspective.

## Solution

The solution to the problem, from the user's perspective.

## Behaviours

A numbered list of the behaviours that must hold at the seams agreed above: what a user, caller, or test does at the seam, and what it observes. Cover the feature's real surface, including the error and edge cases that were discussed. Each entry is checkable: a test or a demo could confirm it.

## Implementation Decisions

A list of implementation decisions that were made. Anything behind a seam that a behaviour above depends on but cannot observe belongs here. This can include:

- The modules that will be built/modified
- The interfaces of those modules that will be modified
- Technical clarifications from the developer
- Architectural decisions
- Schema changes
- API contracts
- Specific interactions

Every decision is binding and unhedged. Open each one with the behaviour number(s) it serves in bold, so a ticket cut from one behaviour can pick up exactly the decisions that bind it:

- **Behaviour 7.** Swipe travel is stage width plus the shortfall below the minimum gap, so charts never pass closer than that gap mid-slide.
- **Behaviours 3, 11.** Overlays suppress gestures only through the overlay controller's flag; no DOM probing.

A decision that serves several behaviours lists them all. A decision that serves none is either a behaviour that was never written down (add it above) or not a decision.

Decisions say what will be true when the work lands. What you currently believe about the codebase (where something lives, why it behaves the way it does today) is not a decision; it goes under Believed Context below. Keeping the two apart matters because an implementer must honour a decision but should check a belief.

Do NOT include specific file paths or code snippets. They may end up being outdated very quickly.

Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it within the relevant decision and note briefly that it came from a prototype. Trim to the decision-rich parts, not a working demo, just the important bits.

## Testing Decisions

A list of testing decisions that were made. Include:

- A description of what makes a good test (only test external behavior, not implementation details)
- Which modules will be tested
- Prior art for the tests (i.e. similar types of tests in the codebase)

## Out of Scope

A description of the things that are out of scope for this spec.

## Believed Context

Factual claims about the codebase that the decisions above rest on: where a piece of state lives, what a current behaviour is caused by, which module already owns a concern. Tag each with the behaviour number(s) it informs, the same way as the decisions. These are beliefs, not decisions: an implementer verifies them against the code before designing around them, and reports back when one is wrong rather than trying to reconcile the spec with it.

Keep this section short. If it is long, the spec was written without reading the code, and the beliefs should be checked now rather than left for every implementer to check later. Omit the section entirely when there is nothing in it.

## Further Notes

Any further notes about the feature.

</spec-template>
