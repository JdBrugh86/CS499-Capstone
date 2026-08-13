---
layout: page
title: Enhancement Two
permalink: /enhancement-two/
---

# Enhancement Two — Algorithms and Data Structures

<div markdown="1">
<span class="outcome-tag">Algorithms &amp; Data Structures</span>
<span class="outcome-tag">Security</span>
</div>

Where Enhancement One hardened the program around the binary search tree, Enhancement Two goes into the tree itself. It implements the delete operation the prior versions only declared, corrects a structural flaw in how the tree handled duplicate keys, normalizes course IDs so comparisons are consistent, and replaces manual pointer management with `std::unique_ptr`-based ownership.

## Files

<ul class="file-list">
  <li>
    <span class="file-label">Enhanced Source</span>
    <span class="file-desc">The Enhancement Two implementation.</span>
    <br>
    <a href="https://github.com/JdBrugh86/CS-499_Capstone/blob/main/Enhancement_Two%20-%20Algorithms_and_Data_Structure/jBrugh_enhancementTwo.cpp">jBrugh_enhancementTwo.cpp</a>
  </li>
  <li>
    <span class="file-label">Enhancement Narrative</span>
    <span class="file-desc">The full write-up of what changed and why.</span>
    <br>
    <a href="https://github.com/JdBrugh86/CS-499_Capstone/blob/main/Enhancement_Two%20-%20Algorithms_and_Data_Structure/Enhancement_Two_Narrative.pdf">Enhancement_Two_Narrative.pdf</a>
  </li>
  <li>
    <span class="file-label">README</span>
    <span class="file-desc">Build/run instructions and a full design comparison against Enhancement One.</span>
    <br>
    <a href="https://github.com/JdBrugh86/CS-499_Capstone/blob/main/Enhancement_Two%20-%20Algorithms_and_Data_Structure/README.md">README.md</a>
  </li>
  <li>
    <span class="file-label">Previous Version (Reference)</span>
    <span class="file-desc">Enhancement One's source, archived here for direct comparison.</span>
    <br>
    <a href="https://github.com/JdBrugh86/CS-499_Capstone/blob/main/Enhancement_Two%20-%20Algorithms_and_Data_Structure/artifactArchive/jBrugh_cs499EnhancementOneArchive.cpp">jBrugh_cs499EnhancementOneArchive.cpp</a>
  </li>
  <li>
    <span class="file-label">Sample Input Data</span>
    <span class="file-desc">CSV of ABCU courses and prerequisites used to exercise the program.</span>
    <br>
    <a href="https://github.com/JdBrugh86/CS-499_Capstone/blob/main/Enhancement_Two%20-%20Algorithms_and_Data_Structure/CS300_ABCU_Advising_Program_Input.csv">CS300_ABCU_Advising_Program_Input.csv</a>
  </li>
</ul>

## Key Improvements

- **A complete delete operation.** `removeNode()` now implements the full recursive BST deletion algorithm, correctly handling the no-child, one-child, and two-child cases via an in-order successor found by `getSuccessor()`.
- **Duplicate-key correction.** The original insertion logic only checked `<`, so re-inserting an existing courseId created a second, structurally invalid node. `addNode()` now checks for an exact match first and updates in place, preserving the invariant that every key appears once.
- **Consistent ordering keys.** Every courseId is passed through a `ToUpper()` helper before insertion, search, update, or deletion, so `CS300` and `cs300` are always treated as the same course.
- **RAII-based memory ownership.** `Node` now uses `std::unique_ptr<Node> left/right` instead of raw pointers. The tree deallocates itself automatically; no destructor or manual `deleteBST()` is needed.
- **Move semantics.** `Course` objects, which hold a `std::vector<std::string>` of prerequisites, are passed through `Insert()` and `addNode()` with `std::move` rather than copied at every level of recursion.
- **A unified update path.** A new `updateCourse()` reuses the same traversal logic as `Search()` to overwrite an existing course's data in place, without duplicating the tree's comparison logic elsewhere.

## A Look at the Duplicate-Key Fix

This is the correctness bug with the most consequence: without it, `Search()` and `InOrder()` behave unpredictably for any courseId that's inserted twice, since a valid BST assumes exactly one node per key.

```cpp
void BinarySearchTree::addNode(Node* node, Course course) {

    // if duplicate course is found, overwrites old node data
    if (course.courseId == node->course.courseId) {
        node->course = std::move(course);  // update instead of duplicating
        return;
    }

    // if current node courseId is larger than new courseId
    if (course.courseId < node->course.courseId) {
        // ...
```

Checking for an exact match before descending further is a small addition, but it's what restores the tree's core invariant, that every key maps to exactly one position, and it's what makes the add-or-update workflow in the menu safe to rely on.
