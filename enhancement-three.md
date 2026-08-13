---
layout: page
title: Enhancement Three
permalink: /enhancement-three/
---

# Enhancement Three — Databases

<div markdown="1">
<span class="outcome-tag">Databases</span>
<span class="outcome-tag">Security</span>
<span class="outcome-tag">Software Engineering</span>
</div>

Enhancement Three is a fundamentally different kind of change from the first two. Where Enhancement One hardened the console program and Enhancement Two corrected the binary search tree itself, in both versions the course catalog still lived and died with the process, every course existed only in memory, and closing the program erased it. Enhancement Three removes the BST entirely and replaces it with a MongoDB-backed `CourseRepository` class built on the official `mongocxx` driver, so the catalog is now a persistent collection that exists independently of any single run of the program.

## Files

<ul class="file-list">
  <li>
    <span class="file-label">Enhanced Source</span>
    <span class="file-desc">The Enhancement Three implementation.</span>
    <br>
    <a href="https://github.com/JdBrugh86/CS-499_Capstone/blob/main/Enhancement_Three%20-%20Databases/jBrugh_enhancementThree.cpp">jBrugh_enhancementThree.cpp</a>
  </li>
  <li>
    <span class="file-label">Enhancement Narrative</span>
    <span class="file-desc">The full write-up of what changed and why.</span>
    <br>
    <a href="https://github.com/JdBrugh86/CS-499_Capstone/blob/main/Enhancement_Three%20-%20Databases/jBrugh_Enhancement_Three_Narrative.docx">jBrugh_Enhancement_Three_Narrative.docx</a>
  </li>
  <li>
    <span class="file-label">README</span>
    <span class="file-desc">Build/run instructions, MongoDB/mongocxx prerequisites, and a full design comparison against Enhancement Two.</span>
    <br>
    <a href="https://github.com/JdBrugh86/CS-499_Capstone/blob/main/Enhancement_Three%20-%20Databases/README.md">README.md</a>
  </li>
  <li>
    <span class="file-label">Previous Version (Reference)</span>
    <span class="file-desc">Enhancement Two's source, archived here for direct comparison.</span>
    <br>
    <a href="https://github.com/JdBrugh86/CS-499_Capstone/blob/main/Enhancement_Three%20-%20Databases/artifactArchive/jBrugh_enhancementTwo.cpp">jBrugh_enhancementTwo.cpp</a>
  </li>
  <li>
    <span class="file-label">Sample Input Data</span>
    <span class="file-desc">CSV of ABCU courses and prerequisites, now used to seed the database.</span>
    <br>
    <a href="https://github.com/JdBrugh86/CS-499_Capstone/blob/main/Enhancement_Three%20-%20Databases/CS300_ABCU_Advising_Program_Input.csv">CS300_ABCU_Advising_Program_Input.csv</a>
  </li>
</ul>

## Key Improvements

- **Persistence.** Course data is written to a MongoDB collection (`snhu_courses.courses`) that lives outside the program's process and survives program exit, instead of disappearing the moment `main()` returns.
- **Database-enforced uniqueness.** `CreateUniqueIndex()` builds a unique index on `courseId` directly on the collection, so the constraint is enforced by MongoDB itself for every write, not just the ones that happen to go through a specific application function.
- **Atomic upsert.** The two-step "search, then insert-or-update" pattern from Enhancement Two becomes a single atomic `replace_one()` call with `upsert(true)`, closing the window where data could change between the read and the write.
- **Query-driven ordering.** `PrintAllCourses()` issues a `find({})` query with a sort on `courseId`, letting MongoDB's query engine produce sorted output instead of relying on a hand-maintained tree shape.
- **Deliberate schema design.** `courseToDocument()` embeds `preReqs` as a nested array within the course document, an appropriate document-modeling choice for data that's always read and written together with its parent course.
- **Bulk-write resilience.** `SeedDbFromCSV()` batches every CSV row into one `insert_many()` call with `ordered(false)`, so a single bad or duplicate row doesn't stop the rest of the load, and the program reports exactly how many rows were saved.
- **Explicit failure handling for an external system.** Every database call is wrapped in a `try`/`catch` for `mongocxx::exception`, and a `ping` command verifies connectivity before the program does any real work, an entire category of failure that doesn't exist when your data structure is just memory.

## A Look at the Atomic Upsert

This is the clearest example of moving a correctness guarantee out of application code and into the database itself:

```cpp
bool CourseRepository::UpsertCourse(const Course& course) {

    try {
        auto filter = bsoncxx::builder::stream::document{}
            << "courseId" << course.courseId
            << bsoncxx::builder::stream::finalize;

        mongocxx::options::replace replaceOpts{};
        replaceOpts.upsert(true);

        auto result = collection.
                        replace_one(filter.view(),
                                    courseToDocument(course), replaceOpts);

        if (result->upserted_id()) {
            std::cout << "Course " << course.courseId << " added." << std::endl;
        }

        else if (result->modified_count() > 0) {
            std::cout << "Course " << course.courseId << " updated." << std::endl;
        }
        // ...
```

One call to `replace_one()` with `upsert(true)` lets MongoDB decide, atomically, whether to insert a new document or replace an existing one, and reports back which happened, replacing a read-then-write pattern that isn't safe in general with a single database-guaranteed operation.

## A Note on Dependencies

Unlike Enhancements One and Two, this version is not self-contained: it requires a running MongoDB instance and the `mongocxx`/`bsoncxx` driver libraries. That's the normal cost of persistence, and it's covered in more detail, including build instructions, in the [README](https://github.com/JdBrugh86/CS-499_Capstone/blob/main/Enhancement_Three%20-%20Databases/README.md).
