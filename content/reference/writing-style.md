---
title: Writing Style
weight: 2
---

# Writing Style

## Audience

Write for capable peers: mid-level developers who can research concepts, make implementation choices, and work on complex problems.

State what the system needs to accomplish, not how to build it. Point to possible approaches or considerations, but let readers make implementation choices.

## Pedagogy

Structure learning through progressive disclosure. Start each challenge with the simplest version of a problem, then add complexity only after the foundation is solid. Make each stage build on previous work so the progression feels natural rather than arbitrary.

Introduce one major capability per stage: one testable system behavior that builds on previous work. Supporting concepts can appear as needed, but each stage should add one clear building block to the system.

Constrain interfaces and contracts that affect testing or compatibility. Leave internal implementation details (data structures, algorithms, optimizations) to the reader's judgment.

Link to external resources when you introduce concepts. Point to tutorials, papers, lectures, or reference implementations - whatever explains it best. Don't replicate explanations that already exist; point to the best resource and move on.

## Voice & Tone

Address the reader directly using second person ("you") and active voice. Write as if you're giving clear directions to a colleague, not lecturing from a podium. Use simple, everyday language.

Get to the point immediately. Skip introductory context about why topics are important - your reader already has that context. Brief section transitions are fine, but avoid elaborate setup. Do explain the reasoning behind specific constraints or design choices you're imposing.

Maintain a matter-of-fact tone. Skip reassurance and cheerleading. An occasional emoji or light moment is fine, but default to straightforward instruction.

## Formatting

Structure each stage with a brief introduction (one or two sentences on what the reader will build), followed by precise specifications, implementation guidance, and testing instructions. Optionally include a debugging section with example test failures or debugging techniques. When showing failures, provide actionable guidance: expected versus actual output, then what to check or what might have gone wrong.

When defining API contracts, data formats, or expected behaviors, be exact. Include complete endpoint specifications with methods, parameters, responses, and literal error messages. Specify data constraints explicitly. The goal is removing ambiguity about _what_ to build without prescribing _how_ to build it.

Use code blocks to show test invocations, command-line usage, and expected outputs - not implementation code or algorithms. Readers should see how to verify their work and what correct behavior looks like.

Reserve callouts for critical non-obvious concerns. Don't use them for general information that belongs in body text.

Link concepts inline when they first appear. Add comprehensive resources like books, lecture series, & reference implementations to the challenge's index page.
