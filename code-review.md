---
layout: page
title: Code Review
permalink: /code-review/
---

# Code Review

Before any enhancement work began, I completed a structured code review of the original artifact, a console program that reads a CSV of course data into a binary search tree and lets a user search for and display course information. The review is intentionally specific and prioritized: rather than general observations, it walks through the source, embedding comments at the exact points where the code fell short, missing input validation, dead code, inconsistent documentation, an unimplemented delete operation, and a data-integrity bug in CSV parsing, and then those same annotations became the roadmap for the three enhancements that followed.

<div class="video-wrapper">
<iframe width="100%" height="400" src="https://www.youtube.com/embed/lHncrTzdZWY" title="CS-499 Capstone Code Review" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

## Source Files

<ul class="file-list">
  <li>
    <span class="file-label">Original Artifact</span>
    <span class="file-desc">The unmodified program as it existed before this capstone's review and enhancements.</span>
    <br>
    <a href="https://github.com/JdBrugh86/CS-499_Capstone/blob/main/jBrugh_CS300_originalArtifact/jBrugh_CS300_ProjectTwo.cpp">jBrugh_CS300_ProjectTwo.cpp</a>
  </li>
  <li>
    <span class="file-label">Annotated Code Review</span>
    <span class="file-desc">The same program with inline review comments marking every design and correctness gap addressed by the enhancements.</span>
    <br>
    <a href="https://github.com/JdBrugh86/CS-499_Capstone/blob/main/Enhancement_One%20-%20Software_Design_and_Engineering/artifactArchive/jBrugh_C499_originalArtifactCodeReview.cpp">jBrugh_C499_originalArtifactCodeReview.cpp</a>
  </li>
  <li>
    <span class="file-label">Sample Input Data</span>
    <span class="file-desc">The ABCU course/prerequisite CSV used to exercise the program throughout the review and every enhancement.</span>
    <br>
    <a href="https://github.com/JdBrugh86/CS-499_Capstone/blob/main/jBrugh_CS300_originalArtifact/CS300_ABCU_Advising_Program_Input.csv">CS300_ABCU_Advising_Program_Input.csv</a>
  </li>
</ul>

## What the Review Found

- **No input validation.** A bad file path or an out-of-range menu choice was handled silently, with no feedback to the user and no recovery path.
- **A data-integrity gap in CSV parsing.** Empty prerequisite fields were dropped rather than recorded, so a course's internal state didn't reliably reflect the source data.
- **Dead code.** `PostOrder()` and `PreOrder()` traversals were declared and implemented but never called.
- **An unimplemented delete/update path.** `removeNode()` was declared in the class but never implemented, and manual `new`/`delete` pointer management left the door open to memory-management bugs.
- **Inconsistent documentation.** Several methods had no header comments, and the comments that did exist were often TODO-style notes rather than descriptions of actual behavior.

Each of these findings maps directly to one of the three enhancements: [Enhancement One]({{ "/enhancement-one/" | relative_url }}) closes the input-validation and documentation gaps, [Enhancement Two]({{ "/enhancement-two/" | relative_url }}) rebuilds the tree itself to add deletion and fix the data structure's correctness guarantees, and [Enhancement Three]({{ "/enhancement-three/" | relative_url }}) moves the data out of memory entirely and into a persistent, indexed database.
