---
layout: page
title: Self-Assessment
permalink: /self-assessment/
---

# Professional Self-Assessment

<div markdown="1">
<span class="outcome-tag">| Collaboration |</span>
<span class="outcome-tag">| Communication |</span>
<span class="outcome-tag">| Algorithms &amp; Data Structures |</span>
<span class="outcome-tag">| Software Design &amp; Engineering |</span>
<span class="outcome-tag">| Security |</span>
</div>

## Introduction

I am a Repair Process Engineer for a major aviation turbine engine manufacturer, and I am completing this Computer Science program to move into embedded software engineering, specifically building and modernizing safety features for motorcycles, an area where a lot of older platforms have never caught up to what modern embedded systems can do. That combination, an engineering background grounded in mechanical systems and safety-critical processes, paired with a computer science education, is the lens through which I approached every artifact in this ePortfolio. My work in a highly regulated manufacturing, repair, and overhaul environment has already taught me that a design decision is only as good as your ability to explain why you made it, to document what could go wrong, and to prove that it doesn't. That discipline is exactly what I tried to bring into this capstone's three enhancements, and it's what I intend to carry into embedded safety systems work going forward.

This ePortfolio centers on a single application: a course advising program that started as a working but rough console tool, backed by a hand-rolled binary search tree, and evolved over three enhancements into a defensively coded, algorithmically correct, database-backed CRUD application. Below, I walk through how the process of building it, and the code review, narratives, and documentation that came with it, demonstrate the program outcomes.

## Collaborative Environments and Organizational Decision-Making

I don't have a formal team project to point to from this program, and I want to be upfront about that rather than overstate it. What I do have is direct experience with the kind of artifact that collaboration in software engineering actually runs on: a structured code review. My [code review]({{ "/code-review/" | relative_url }}) of the original artifact is written the way I'd want a review to read if I were the one inheriting the code. It's specific, prioritized by what actually affects correctness or maintainability, and framed as next steps rather than blame. That review became the literal roadmap for all three enhancements that followed it, which is the same function a code review or design document serves inside a real engineering organization: it turns one person's judgment into a shared, actionable plan that other people, or a future version of yourself, can act on without having to reconstruct the reasoning from scratch.

In my current role, I industrialize repairs at my facility, write process documentation, and perform failure investigations that get read by people with varying technical backgrounds, including mechanics, quality engineers, and program managers. The decisions that documentation drives are organizational ones: whether a repair process can be accomplished at my facility and what it would take to perform it, whether a repair process is approved, or whether a deviation is acceptable. Translating a technical finding into something a non-specialist can act on confidently is a skill I already practice daily, and it's the same skill a self-assessment, a narrative document, or a code review exercises. I see this capstone's artifacts as evidence that I can extend that same discipline into a software context.

## Professional-Quality Communication

Each enhancement in this portfolio is paired with a written narrative that explains not just what changed, but why, what problem it solved, and what trade-off or limitation remains. I deliberately wrote those narratives for two audiences at once: someone evaluating the technical correctness of the change, and someone trying to understand the engineering judgment behind it. The README files that accompany each version of the program are aimed at a third audience entirely, a future developer or user who just wants to build and run the thing, so they're written as plain, practical, step-by-step instructions rather than a technical narrative.

That range, code comments written for a maintainer, narratives written for an evaluator, and README documentation written for a user, reflects a habit I try to hold myself to, which is identifying who is actually going to read a piece of communication and write for that person, not for myself. In a safety-relevant embedded systems context, this matters enormously, because a specification, a hazard analysis, or a design review document that fails to reach its intended audience is a communication failure with real consequences, not just an inconvenience.

## Algorithmic Design and Trade-Off Management

The clearest evidence of this outcome is [Enhancement Two]({{ "/enhancement-two/" | relative_url }}), where I implemented full recursive BST deletion; correctly handling the no-child, one-child, and two-child cases using an in-order successor; fixed a duplicate-key bug that would have silently corrupted the tree's structure; and normalized key comparisons so the tree's core correctness guarantee, that every key maps to exactly one node, actually held under real-world input. None of that is glamorous work, but it's the kind of defect that only surfaces when a data structure is exercised the way it would actually be used in practice through repeated inserts, deletions requiring restructuring, and searches whose correctness depends on invariants holding everywhere in the tree, and not just on the happy path.

I was also explicit about what I did not solve. The tree remains unbalanced, so its operations degrade toward O(n) in the worst case, and I documented that limitation rather than let it go unstated. Being clear about a design's boundaries is, in my view, as much a part of algorithmic competence as the implementation itself; a solution that quietly hides its own weaknesses is more dangerous than one that names them.

## Tools and Techniques That Deliver Value

[Enhancement Three]({{ "/enhancement-three/" | relative_url }}) replaces the in-memory BST entirely with a MongoDB-backed repository using the mongocxx driver, and the choice wasn't just "add a database," it was about moving specific correctness guarantees to where they belong. Course ID uniqueness moved from an application-level check to a database-enforced unique index. An insert-or-update workflow that used to be two round trips and a manual branch became one atomic upsert. Sorting moved from a hand-maintained tree shape to a database query. Each of those decisions reflects a broader principle I want to carry into embedded work: use the tool that is actually built to solve the problem, rather than reimplementing that tool's job by hand, or reinventing the wheel as they say, because every line of hand-rolled logic is a line that has to be tested, maintained, and eventually proven correct by someone.

## Security Mindset

Security is the thread that runs through all three enhancements, and it's also the outcome I most directly connect to my career goal, because safety-critical embedded systems and secure systems share the same core assumption: input from the outside world, a sensor, a user, a file, will eventually be malformed, malicious, or simply wrong, and the system has to survive that gracefully rather than fail silently or unpredictably, especially when moving at speed.

Concretely, that shows up as file-path validation that rejects absolute paths and directory traversal attempts before a file is ever opened, a sanitization routine that strips control characters and bounds every field's length before it's stored or displayed, and, in Enhancement Three, moving the uniqueness constraint into the database itself so it's enforced for every write, not just the ones that happen to go through a specific function I remembered to write correctly. None of these are exotic techniques. What I want this portfolio to demonstrate is that I default to treating input as untrusted and to closing gaps at the point where they're introduced, rather than patching around them downstream, which is exactly the posture I'll need when the "input" is a sensor reading or a CAN bus message instead of a CSV file.

## Conclusion

Altogether, these three enhancements and the artifacts around them, the code review, the narratives, the READMEs, aren't meant to show that I fixed a small console program. They're meant to show a consistent way of working: identify the real gaps through honest self-review, prioritize the ones that affect correctness and trust, fix them with the technique appropriate to the problem, and document what's done and what's deliberately left for later. That's the same process I intend to bring to embedded software for motorcycle safety systems, where the stakes for getting input validation, data integrity, and honest documentation of limitations right are considerably higher than a course catalog.
