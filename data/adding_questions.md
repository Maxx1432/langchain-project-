You have **three flexible ways** to add quiz questions. You can choose whichever approach fits your authoring workflow best:

---

### Method 1: In `course-structure.md` (Recommended & Easiest)

This is the standard approach used across the platform. Under any lesson heading (`## XX. Lesson Title`), add a `- **Knowledge Check**:` block:

```markdown
## 03. Investigating Jenkins Builds and Failures

- **Summary**: How to inspect a Jenkins build number, use Console Output for troubleshooting...
- **Context**:
  - lessons/03-build-troubleshooting/01-build-details-and-console-output.md
  - lessons/03-build-troubleshooting/02-build-page-options.md
- **Knowledge Check**:
  - **Question**: What is usually the first Jenkins page to inspect when a build fails?
  - **Type**: multiple_choice
  - **Options**:
    - Changes
    - Parameters
    - [x] Console Output
    - Previous Build
  - **Explanation**: Console Output contains the detailed execution logs and is usually the primary source for identifying the failure reason.
```

#### Syntax Rules:

1. **Mark the correct answer** by prefixing it with `[x]` (e.g., `- [x] Console Output`).
2. **Incorrect options** can either be plain `- Changes` or `- [ ] Changes`.
3. **`- **Explanation**:`** is shown to the engineer immediately after clicking **Submit Answer** (colored green for correct, rose/red for incorrect).

---

### Method 2: In a Dedicated `knowledge-check.md` File in the Lesson Folder

If you prefer to keep `course-structure.md` lean and store the quiz directly with the lesson's materials:

1. Create a file named `knowledge-check.md` (or `quiz.md`) in the lesson's folder:

   ```
   rag_service/knowledge_cafe/courses/jenkins-deployment-pipelines-kt/lessons/
   └── 03-build-troubleshooting/
       ├── 01-build-details-and-console-output.md
       ├── 02-build-page-options.md
       └── knowledge-check.md   <-- Add here
   ```

2. Inside `knowledge-check.md`, write:

   ```markdown
   ## Knowledge Check

   - **Question**: What is usually the first Jenkins page to inspect when a build fails?
   - **Type**: multiple_choice
   - **Options**:
     - Changes
     - Parameters
     - [x] Console Output
     - Previous Build
   - **Explanation**: Console Output contains the detailed execution logs and is usually the primary source for identifying the failure reason.
   ```

If no quiz is defined in `course-structure.md`, `CourseLoader` automatically looks in the lesson folder for this file.

---

### Method 3: Embedded at the Bottom of Any Context File

You can also paste the `## Knowledge Check` block at the bottom of any existing context file in that lesson (e.g., at the end of `01-build-details-and-console-output.md`). `CourseLoader` will detect and parse it automatically.

---

### Useful Things to Know

- **Optional per Lesson**: If a lesson does not have a quiz defined, the **Knowledge Check** card in the UI will simply be hidden for that lesson.
- **Instant Hot-Reload**: You don't need to rebuild or run migrations when adding or changing questions. Because questions are read directly from disk, saving the file and refreshing the lesson page in your browser immediately displays the new question.
- **Evaluation**: The answer is evaluated deterministically in the backend without calling any LLM. When an engineer submits an answer, their score and submission timestamp are saved to SQLite under their course enrollment.
