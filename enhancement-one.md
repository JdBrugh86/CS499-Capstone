---
layout: page
title: Enhancement One
permalink: /enhancement-one/
---

# Enhancement One — Software Design and Engineering

<div markdown="1">
<span class="outcome-tag">Software Engineering</span>
<span class="outcome-tag">Communication</span>
<span class="outcome-tag">Security</span>
</div>

Enhancement One keeps the original artifact's core approach, a binary search tree keyed on `courseId`, but closes nearly every gap the [code review]({{ "/code-review/" | relative_url }}) identified. It treats user input as untrustworthy by default, fixes a data-integrity bug in CSV parsing, trims the class's public interface down to what the program actually uses, and replaces sparse, TODO-style comments with documentation written for the next person who reads the code.

## Files

<ul class="file-list">
  <li>
    <span class="file-label">Enhanced Source</span>
    <span class="file-desc">The Enhancement One implementation.</span>
    <br>
    <a href="https://github.com/JdBrugh86/CS-499_Capstone/blob/main/Enhancement_One%20-%20Software_Design_and_Engineering/jBrugh_cs499EnhancementOne.cpp">jBrugh_cs499EnhancementOne.cpp</a>
  </li>
  <li>
    <span class="file-label">Enhancement Narrative</span>
    <span class="file-desc">The full write-up of what changed and why.</span>
    <br>
    <a href="https://github.com/JdBrugh86/CS-499_Capstone/blob/main/Enhancement_One%20-%20Software_Design_and_Engineering/jBrugh_Enhancement_One_Narrative.pdf">jBrugh_Enhancement_One_Narrative.pdf</a>
  </li>
  <li>
    <span class="file-label">README</span>
    <span class="file-desc">Build/run instructions and a full design comparison against the original artifact.</span>
    <br>
    <a href="https://github.com/JdBrugh86/CS-499_Capstone/blob/main/Enhancement_One%20-%20Software_Design_and_Engineering/README.md">README.md</a>
  </li>
  <li>
    <span class="file-label">Sample Input Data</span>
    <span class="file-desc">CSV of ABCU courses and prerequisites used to exercise the program.</span>
    <br>
    <a href="https://github.com/JdBrugh86/CS-499_Capstone/blob/main/Enhancement_One%20-%20Software_Design_and_Engineering/CS300_ABCU_Advising_Program_Input.csv">CS300_ABCU_Advising_Program_Input.csv</a>
  </li>
</ul>

## Key Improvements

- **Defensive file loading.** Before any read is attempted, `std::filesystem::exists(csvPath)` confirms the file is actually there. If it's not, the program gives the user up to three chances to enter a corrected path, re-validating both existence and the `.csv` extension on each attempt.
- **Root-cause fix for missing prerequisites.** The original parser silently dropped empty prerequisite fields. Enhancement One captures them explicitly and stores `"None"`, so a `Course` object's state is always well-defined regardless of how its CSV row is formatted.
- **Reduced interface surface.** `PostOrder()`, `PreOrder()`, and their private helpers, all unused, are removed entirely, leaving a public interface that maps exactly to the operations the program uses.
- **Separation of concerns.** The console menu is extracted into `userMenu()` and `topBottomMenuBorder()`, decoupling presentation from the control flow in `main()`.
- **Documentation as a design artifact.** Every class, struct, and non-trivial method carries a comment describing actual purpose and behavior, not a TODO reminder.

## A Look at the Validation Logic

The clearest example of the shift from a "happy path" program to a defensive one is how a bad file path is handled. Instead of letting `ifstream` fail silently, the program checks the extension explicitly and gives the user real feedback:

```cpp
// Checks if file is a valid CSV file
bool checkCSV(std::string file) {
    std::string fileToCheck = file;
    std::string checkForCSV = ".csv";
    int result = fileToCheck.find(checkForCSV);

    // Returns true if file is a CSV
    if (result != std::string::npos) {
        return(1);
    }

    // Returns false if file is not a CSV
    else {
        return(0);
    }
}
```

This check, combined with a `std::filesystem::exists()` guard and a retry loop in `loadCSV()`, means a bad path now produces a clear message and a second chance instead of a program that silently does nothing.
