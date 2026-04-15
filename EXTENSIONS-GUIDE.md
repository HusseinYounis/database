# Quarto Extensions Guide
## Fundamentals of Database Systems — Book Project

This guide covers all 13 installed extensions: how to use them in chapters, which output formats they support, and how to add or modify chapters.

---

## Table of Contents

1. [Quick Reference](#quick-reference)
2. [Extension Details & Examples](#extension-details--examples)
   - [1. interactive-sql](#1-interactive-sql)
   - [2. pseudocode](#2-pseudocode)
   - [3. line-highlight](#3-line-highlight)
   - [4. quizdown](#4-quizdown)
   - [5. fontawesome](#5-fontawesome)
   - [6. highlight-text](#6-highlight-text)
   - [7. collapse-callout](#7-collapse-callout)
   - [8. callouty-theorem](#8-callouty-theorem)
   - [9. details](#9-details)
   - [10. accordion](#10-accordion)
   - [11. elevator](#11-elevator)
   - [12. epigraph](#12-epigraph)
   - [13. badge](#13-badge)
3. [Chapter Management](#chapter-management)
   - [Add a New Chapter](#add-a-new-chapter)
   - [Modify an Existing Chapter](#modify-an-existing-chapter)
   - [Add a New Part (Section)](#add-a-new-part-section)
4. [Global Configuration Reference](#global-configuration-reference)

---

## Quick Reference

| # | Extension | Type | HTML | PDF | Usage |
|---|-----------|------|------|-----|-------|
| 1 | `shafayetShafee/interactive-sql` | filter | ✅ | ❌ | Run SQL in browser |
| 2 | `leovan/pseudocode` | filter | ✅ | ✅ | Algorithm pseudocode |
| 3 | `shafayetShafee/line-highlight` | filter | ✅ | ❌ | Highlight code lines |
| 4 | `parmsam/quizdown` | filter | ✅ | ❌ | Interactive quizzes |
| 5 | `quarto-ext/fontawesome` | shortcode | ✅ | ✅ | FA icons |
| 6 | `mcanouil/highlight-text` | filter | ✅ | ✅ | Text color/highlight |
| 7 | `shafayetShafee/collapse-callout` | filter | ✅ | ❌ | Collapsible callouts |
| 8 | `sun123zxy/callouty-theorem` | filter | ✅ | ✅ | Theorem blocks |
| 9 | `jmgirard/details` | shortcode | ✅ | ❌ | Collapsible hints |
| 10 | `royfrancis/accordion` | shortcode | ✅ | ❌ | Accordion panels |
| 11 | `mcanouil/elevator` | shortcode | ✅ | ❌ | Back-to-top button |
| 12 | `andrewheiss/epigraph` | shortcode | ✅ | ✅ | Chapter epigraphs |
| 13 | `mcanouil/badge` | shortcode | ✅ | ❌ | Status badges |

**Filters** are active globally via `_quarto.yml`. **Shortcodes** are available anywhere without further configuration.

---

## Extension Details & Examples

---

### 1. `interactive-sql`

**Purpose:** Embeds an in-browser SQL engine (SQL.js / DuckDB) so readers can run queries live in the HTML book — no server needed.

**Supported formats:** HTML only

**Setup per chapter:** Add to the chapter's YAML front matter:

```yaml
---
title: "Chapter 7: SQL Basics"
filters:
  - interactive-sql
---
```

Or rely on the global filter already added in `_quarto.yml` (`filters` under `html:`).

**Usage — interactive SQL block:**

````markdown
```{.sql .interactive #ex-select}
SELECT name, salary
FROM employee
WHERE salary > 50000
ORDER BY salary DESC;
```
````

- `.interactive` makes the block runnable
- `#ex-select` is an optional anchor ID

**Provide a database for the block** by defining it above or in a setup block:

````markdown
```{.sql .interactive .dbname="company" setup="true"}
CREATE TABLE employee (
  ssn CHAR(9) PRIMARY KEY,
  name VARCHAR(50),
  salary DECIMAL(10,2)
);
INSERT INTO employee VALUES ('123456789', 'John Smith', 65000);
INSERT INTO employee VALUES ('333445555', 'Joy Wong', 75000);
```

Now run a query against it:

```{.sql .interactive .dbname="company"}
SELECT name, salary FROM employee WHERE salary > 60000;
```
````

**Tip:** Group related setup + query blocks using the same `.dbname` value.

---

### 2. `pseudocode`

**Purpose:** Renders algorithm pseudocode using the `pseudocode.js` library (HTML) or the `algorithm2e` LaTeX package (PDF). Numbered line support, if/while/for constructs.

**Supported formats:** HTML ✅ | PDF ✅

**Usage:**

````markdown
```pseudocode
#| label: alg-nested-loop-join
#| html-indent-size: "1.2em"
#| html-comment-delimiter: "//"
#| html-line-number: true
#| pdf-placement: "htb!"
#| pdf-line-number: true

\begin{algorithm}
\caption{Nested-Loop Join}
\begin{algorithmic}
\Require Relations $R$, $S$ and join condition $\theta$
\Ensure Result relation $R \bowtie_\theta S$
\State $result \leftarrow \emptyset$
\For{each tuple $t_r$ in $R$}
  \For{each tuple $t_s$ in $S$}
    \If{$t_r \bowtie t_s$ satisfies $\theta$}
      \State $result \leftarrow result \cup \{t_r \cdot t_s\}$
    \EndIf
  \EndFor
\EndFor
\State \Return $result$
\end{algorithmic}
\end{algorithm}
```
````

**Cross-reference:** Use `@alg-nested-loop-join` anywhere to link to it.

---

### 3. `line-highlight`

**Purpose:** Highlights specific lines in code blocks — useful for drawing attention to a particular clause in SQL or a key line in an algorithm.

**Supported formats:** HTML only

**Usage — at the code block level:**

````markdown
```{.sql source-line-numbers="3,5-7"}
SELECT e.fname, e.lname, d.dname
FROM employee e
  JOIN department d          -- highlighted: line 3
  ON e.dno = d.dnumber
WHERE d.dname = 'Research'   -- highlighted: lines 5–7
  AND e.salary > 40000
ORDER BY e.lname;
```
````

Line numbers can be:
- Single: `"3"`
- Range: `"5-7"`
- Mixed: `"1,3,5-7,10"`

**Add line numbers without highlighting:**

````markdown
```{.sql code-line-numbers="true"}
SELECT * FROM employee;
```
````

---

### 4. `quizdown`

**Purpose:** Embeds interactive multiple-choice quizzes with immediate feedback, scored results, and shuffle options.

**Supported formats:** HTML only

**Usage:**

````markdown
```{quizdown}
---
primaryColor: '#2c6e9e'
shuffleQuestions: false
shuffleAnswers: true
---

# What does SQL stand for?

> Hint: It was originally called SEQUEL.

1. [ ] Structured Query List
1. [x] Structured Query Language
1. [ ] Sequential Query Logic
1. [ ] System Query Language

# Which SQL clause filters rows *after* grouping?

1. [ ] WHERE
1. [x] HAVING
1. [ ] FILTER
1. [ ] GROUP BY

# What normal form eliminates transitive dependencies?

> A relation in 2NF is also required.

1. [ ] 1NF
1. [ ] 2NF
1. [x] 3NF
1. [ ] BCNF
```
````

**Answer syntax:**
- `[x]` = correct answer
- `[ ]` = wrong answer

---

### 5. `fontawesome`

**Purpose:** Inline Font Awesome icons in text, callouts, and headings.

**Supported formats:** HTML ✅ | PDF ✅ (uses fontawesome5 LaTeX package)

**Usage:**

```markdown
{{< fa database >}} A **database** is an organized collection of data.

{{< fa table >}} Tables (relations) store entity instances as rows.

{{< fa lock >}} Transaction properties: {{< fa shield-halved >}} ACID.

{{< fa lightbulb >}} **Tip:** Always verify referential integrity constraints.
```

**Size modifiers:** `size=lg`, `size=2x`, `size=3x`

```markdown
{{< fa database size=2x >}}
```

Find icon names at [fontawesome.com/icons](https://fontawesome.com/icons).

**Useful icons for database topics:**

| Icon | Code | Use |
|------|------|-----|
| 🗄️ | `{{< fa database >}}` | Database |
| 📋 | `{{< fa table >}}` | Table/Relation |
| 🔑 | `{{< fa key >}}` | Primary key |
| 🔗 | `{{< fa link >}}` | Foreign key / join |
| 🔒 | `{{< fa lock >}}` | Transaction lock |
| ⚠️ | `{{< fa triangle-exclamation >}}` | Warning |
| 💡 | `{{< fa lightbulb >}}` | Tip/Hint |
| ✅ | `{{< fa circle-check >}}` | Correct |
| ❌ | `{{< fa circle-xmark >}}` | Incorrect |

---

### 6. `highlight-text`

**Purpose:** Color specific words or phrases inline — works in both HTML and PDF.

**Supported formats:** HTML ✅ | PDF ✅

**Usage:**

```markdown
The [primary key]{fg="#c0392b" .bold} uniquely identifies each tuple.

A [foreign key]{fg="#2980b9"} references the primary key of another relation.

[ACID]{bg="#f39c12" fg="#000000"} stands for Atomicity, Consistency, Isolation, Durability.
```

**Parameters:**
- `fg="#hex"` — foreground (text) color
- `bg="#hex"` — background highlight color
- `.bold` — bold text class (combine with span)

**Suggested color palette:**

| Concept | Color | Code |
|---------|-------|------|
| Key terms | Navy | `fg="#2c3e7a"` |
| Warnings | Red | `fg="#c0392b"` |
| Important | Orange highlight | `bg="#f39c12"` |
| SQL keywords | Blue | `fg="#2980b9"` |
| New terms | Green | `fg="#27ae60"` |

---

### 7. `collapse-callout`

**Purpose:** Makes callout blocks collapsible in HTML output — readers can expand/collapse notes, tips, and warnings.

**Supported formats:** HTML only

**Control globally** (all callouts collapsed by default) — add to chapter front matter or `_quarto.yml` under `html:`:

```yaml
collapse-callout:
  all: true        # collapse all callout types
```

Or selectively by type:

```yaml
collapse-callout:
  note: true
  tip: false
  warning: true
  important: false
```

**Override per callout block:**

```markdown
::: {.callout-note collapse="true"}
## Note: First Normal Form
A relation is in 1NF if every attribute contains only atomic values — no repeating groups.
:::

::: {.callout-tip collapse="false"}
## Tip: Choosing a Primary Key
Prefer surrogate integer keys for performance; use natural keys only when they are stable.
:::
```

**Collapse with custom title:**

```markdown
::: {.callout-warning collapse="true" title="Warning: NULL Semantics"}
NULL is not equal to NULL in SQL. Use `IS NULL` or `IS NOT NULL` — never `= NULL`.
:::
```

---

### 8. `callouty-theorem`

**Purpose:** Wraps theorem, definition, lemma, and proof environments inside styled callout blocks.

**Supported formats:** HTML ✅ | PDF ✅

**Setup per chapter:** Add to the chapter's YAML front matter:

```yaml
---
title: "Chapter 9: Normalization"
filters:
  - callouty-theorem
---
```

**Built-in environments:**

```markdown
::: {#def-functional-dependency .definition}
**Functional Dependency**

A functional dependency $X \to Y$ holds on a relation $R$ if, for every pair of tuples $t_1, t_2$ in $R$: if $t_1[X] = t_2[X]$ then $t_1[Y] = t_2[Y]$.
:::

::: {#thm-armstrongs .theorem}
**Armstrong's Axioms**

The following rules are sound and complete for deriving all functional dependencies:

1. **Reflexivity:** If $Y \subseteq X$, then $X \to Y$
2. **Augmentation:** If $X \to Y$, then $XZ \to YZ$
3. **Transitivity:** If $X \to Y$ and $Y \to Z$, then $X \to Z$
:::

::: {.proof}
By repeated application of Reflexivity, Augmentation, and Transitivity…
:::
```

**Cross-reference:** `@def-functional-dependency`, `@thm-armstrongs`

---

### 9. `details`

**Purpose:** Collapsible `<details>` blocks — ideal for hints, solutions, and supplementary material.

**Supported formats:** HTML only

**Basic shortcode:**

```markdown
{{< dtext "The answer is: SELECT COUNT(*) FROM employee WHERE dno = 5;" summary="Show Answer" >}}
```

**Multi-line content with fenced version:**

```markdown
{{< details summary="Hint: How to find the candidate key" >}}
1. Compute the closure of each attribute subset.
2. A set $K$ is a candidate key if $K^+ = R$ (all attributes) and no proper subset of $K$ has this property.
{{< /details >}}
```

**Nested content (code blocks, lists):**

```markdown
{{< details summary="Solution: Decompose to 3NF" >}}

**Step 1:** Identify all functional dependencies.

**Step 2:** Find the minimal cover.

```sql
-- After decomposition:
CREATE TABLE emp_dept (ssn CHAR(9), dno INT, PRIMARY KEY (ssn));
CREATE TABLE dept (dno INT PRIMARY KEY, dname VARCHAR(50));
```

{{< /details >}}
```

---

### 10. `accordion`

**Purpose:** Bootstrap accordion panels — group multiple collapsible sections under one block, useful for chapter summaries or categorized examples.

**Supported formats:** HTML only

**Usage:**

```markdown
{{< accordion >}}

{{< accordion-item header="DDL: Data Definition Language" >}}
DDL statements define the database schema:
- `CREATE TABLE` — define a new table
- `ALTER TABLE` — modify an existing table
- `DROP TABLE` — delete a table
{{< /accordion-item >}}

{{< accordion-item header="DML: Data Manipulation Language" >}}
DML statements manipulate data:
- `SELECT` — query data
- `INSERT` — add rows
- `UPDATE` — modify rows
- `DELETE` — remove rows
{{< /accordion-item >}}

{{< accordion-item header="DCL: Data Control Language" >}}
DCL statements control access:
- `GRANT` — give privileges
- `REVOKE` — remove privileges
{{< /accordion-item >}}

{{< /accordion >}}
```

**Open first item by default:**

```markdown
{{< accordion-item header="..." open=true >}}
```

---

### 11. `elevator`

**Purpose:** Adds a "back to top" button visible after the reader scrolls down. Place once per chapter or in a shared component.

**Supported formats:** HTML only

**Usage** — place at the bottom of any chapter:

```markdown
{{< elevator label="↑ Back to top" >}}
```

Or with default label:

```markdown
{{< elevator >}}
```

To add to every chapter automatically, add a shared footer file and include it in `_quarto.yml`:

```yaml
format:
  html:
    include-after-body: shared/footer.html
```

`shared/footer.html`:
```html
<p>{{< elevator label="Back to top" >}}</p>
```

---

### 12. `epigraph`

**Purpose:** Typeset chapter-opening quotes (epigraphs) with consistent styling in both HTML and PDF.

**Supported formats:** HTML ✅ | PDF ✅

**Usage:**

```markdown
{{< epigraph "The purpose of abstracting is not to be vague, but to create a new semantic level in which one can be absolutely precise." source="Edsger W. Dijkstra" >}}
```

**With optional link:**

```markdown
{{< epigraph "Data is the new oil." source="Clive Humby, 2006" url="https://example.com" >}}
```

**Place immediately after the chapter title** (before the first paragraph) for proper typesetting:

```markdown
# Chapter 1: Introduction to Databases

{{< epigraph "Without data, you're just another person with an opinion." source="W. Edwards Deming" >}}

## 1.1 What Is a Database?
...
```

---

### 13. `badge`

**Purpose:** Inline status/version badges — useful for marking content as "Advanced", "New in SQL:2016", or linking to resources.

**Supported formats:** HTML only

**Usage (key–value badge):**

```markdown
{{< badge SQL:2016 >}}

{{< badge Difficulty Advanced >}}

{{< badge Status Draft >}}

{{< badge Version 3.0 color=blue >}}
```

**With link:**

```markdown
{{< badge Docs Online href="https://www.postgresql.org/docs/" >}}
```

**Color options:** `blue` (default), `green`, `red`, `orange`, `purple`, `grey`

**Typical uses in this book:**

```markdown
## 8.3 Window Functions {{< badge SQL:2003 >}}

::: {.callout-tip}
{{< badge New >}} The `FILTER` clause on aggregates was added in SQL:2003.
:::
```

---

## Chapter Management

### Add a New Chapter

**Step 1:** Create the `.qmd` file in the appropriate part folder.

```
part-04-sql/ch09-views.qmd
```

**Step 2:** Use this front matter template:

```yaml
---
title: "Views and Virtual Tables"
subtitle: "Chapter 9"
description: "Creating, updating, and managing SQL views."
---
```

**Step 3:** Register the chapter in `_quarto.yml` under the matching part:

```yaml
    - part: "Part IV: Structured Query Language (SQL)"
      chapters:
        - part-04-sql/ch07-sql-basics.qmd
        - part-04-sql/ch08-advanced-sql.qmd
        - part-04-sql/ch09-views.qmd    # ← add here
```

**Step 4:** Rebuild HTML: `quarto render` or `quarto preview`.

---

### Modify an Existing Chapter

1. Open the `.qmd` file in the appropriate `part-XX-*/` folder.
2. Edit content — Markdown, code blocks, callouts, shortcodes all work directly.
3. To preview live: `quarto preview` (hot-reloads on save).
4. To render only one file: `quarto render part-04-sql/ch07-sql-basics.qmd`.
5. To render the full book: `quarto render`.

**Important:** The book uses `freeze: auto` in `_quarto.yml`, so only changed files are re-executed. To force re-execution: `quarto render --no-cache`.

---

### Add a New Part (Section)

**Step 1:** Create the folder:

```
mkdir part-09-advanced/
```

**Step 2:** Create the first chapter file:

```
part-09-advanced/ch16-distributed-db.qmd
```

**Step 3:** Add to `_quarto.yml` chapters list:

```yaml
    - part: "Part IX: Advanced Topics"
      chapters:
        - part-09-advanced/ch16-distributed-db.qmd
```

---

## Global Configuration Reference

The `_quarto.yml` file controls book-wide behavior. Key sections:

### HTML Filters (active for all chapters)

```yaml
format:
  html:
    filters:
      - shafayetShafee/interactive-sql   # Run SQL in browser
      - leovan/pseudocode                # Algorithm blocks
      - shafayetShafee/line-highlight    # Code line highlighting
      - parmsam/quizdown                 # Interactive quizzes
      - mcanouil/highlight-text          # Inline text color
      - shafayetShafee/collapse-callout  # Collapsible callouts
      - sun123zxy/callouty-theorem       # Theorem callout blocks
```

Shortcodes (`fontawesome`, `details`, `accordion`, `elevator`, `epigraph`, `badge`) are available everywhere without filter registration.

### Adding a Chapter-Specific Filter Override

Some filters accept per-chapter YAML options. Add them to the chapter's front matter:

```yaml
---
title: "Chapter 9: Normalization"
collapse-callout:
  note: true
  tip: false
---
```

### Rebuilding the PDF

```bash
quarto render --to pdf
```

The PDF uses `lualatex` with TinyTeX. Extensions that are HTML-only are gracefully ignored during PDF rendering.

### Updating an Extension

```bash
quarto update shafayetShafee/interactive-sql
```

### Listing Installed Extensions

```bash
quarto list extensions
```
