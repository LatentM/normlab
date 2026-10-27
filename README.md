# Normalization Virtual Lab

BACSE202 Database Systems, digital assignment, VIT .
Guide: Dr. Swaminathan A.

The lab does two kinds of normalization. You can give it a relation as attributes and
functional dependencies, the way textbook questions are set. Or you can give it an actual
table of rows, and it works out the dependencies from the data and splits the table for you.
It also has a quiz generator and a bank of GATE previous-year questions on this unit.

It is one HTML file. There is no server, no login and no database behind it.

## Running it

Open `dist/index.html` in Chrome, Edge or Firefox. That's all.

The first time you open it, keep the internet on. The fonts and two libraries load from the
internet: jsPDF for PDF reports and SheetJS for reading uploaded `.xlsx` files. Everything
else works offline, including the Excel, CSV and Word exports.

To serve it instead of opening the file directly:

    cd dist
    python -m http.server 8000      # then open http://localhost:8000

To put it online, drag the `dist` folder onto https://app.netlify.com/drop.

## What's in it

**Lab** (relations). Type a relation such as `R(A,B,C,D,E)` and its dependencies such as
`AB -> C, C -> D, D -> E`. Tabs:

- Normal form: the highest form it satisfies, and the dependency that breaks the next one.
- Closure: X⁺, with each step showing which dependency added which attribute.
- Keys: candidate keys, prime attributes and the number of superkeys.
- Cover: minimal and canonical cover, with each removed attribute or dependency explained.
- Decompose: 3NF synthesis and BCNF decomposition.
- Test: a decomposition you type, checked for lossless join (chase table) and dependency preservation.

**Tables** (real data). Paste a table, upload a CSV or Excel file, or pick one of the five
samples. The page then goes through six steps:

1. Table: edit cells directly.
2. First normal form: lists in a cell (`Robotics; Music`) and repeating columns
   (`course1, grade1, course2, grade2`) are found and flattened, and duplicate rows are removed.
3. Dependencies: the dependencies and keys that hold in the rows. You can type any rule,
   such as `hostel -> warden`, and see whether the data breaks it, with the rows that break it.
4. Diagnose: the normal form, with redundant cells highlighted and the insert, update and
   delete anomalies they cause.
5. Normalize: the 2NF, 3NF, BCNF and 4NF tables with their actual rows. Each set is joined
   back together to confirm you get the original rows and no extra ones.
6. SQL and export: `CREATE TABLE` and `INSERT` statements for MySQL, PostgreSQL, Oracle or
   SQLite, with primary and foreign keys. The tables can also be saved as Excel or as a zip of CSVs.

**Quiz.** Generated questions (closure, implication, keys, superkey count, prime attributes,
normal form, cover, decomposition, and questions on table instances), at three levels. It has
a practice mode and a timed mode marked the GATE way: −⅓ of the marks for a wrong
single-answer question, no negative marks otherwise.

**Competitive exams.** 39 previous-year questions: 35 from GATE CSE, 3 from GATE IT and 1 from UGC NET, spread from
1999 to 2026. Each has the answer key and a worked solution. You can filter by year, topic and
type, bookmark questions, and attempt them as a timed paper. Where a question can be loaded
into the Lab, there is a button for it.

**Learn, Help, Download.** Learn covers the theory each tab uses, with an animated walkthrough.
Download writes a report of the current relation or table as PDF, Word or text.

## A note on finding dependencies from data

A table can show that a dependency is false, because two rows agree on the left side and
differ on the right. It cannot prove that a dependency is true. Ten rows where every
`hostel` has one `warden` don't mean the next row will follow that rule. So the Tables page
treats what it finds as suggestions, and you tick the ones that are real rules. It chooses
sensible defaults for identifier-like columns (`regno`, `order_id`), but you make the final
call. Our viva answer to "how do you know `course -> title` holds?" is that we decided it,
and the data didn't contradict it.

## Limits

- At most 16 attributes in a relation. Key search and superkey counting look at subsets,
  so the work doubles with every attribute added.
- At most 16 columns and 2,000 rows in a table. Dependency mining looks at left sides of up
  to 3 columns (2 when the table has more than 10 columns). Keys can be up to 4 columns.
- 5NF is only reported when Date and Fagin's sufficient condition applies: the relation is in
  4NF and every candidate key is a single attribute. The lab does not search for join dependencies.
- Progress (quiz history, PYQ bookmarks, theme) is saved in the browser. It doesn't carry
  over to another browser or another computer.

## Code

Plain JavaScript, HTML and CSS, with no framework and no npm packages. `build.js` joins the
files in `src/` into `dist/index.html`.

| File | Contents |
|---|---|
| `src/engine.js` | Parsing, closure, keys, covers, normal forms, 3NF/BCNF decomposition, chase, preservation. Attribute sets are bitmasks. |
| `src/table-engine.js` | Table parsing, 1NF repair, dependency and key discovery, projection and join, SQL generation |
| `src/files.js` | ZIP and `.xlsx` writers used by the exports (this is why Excel and Word export work offline) |
| `src/table-examples.js` | The five sample tables |
| `src/pyq.js` | The PYQ bank: question, options, answer, solution |
| `src/app-lab.js` | Lab page and the dependency diagram |
| `src/app-table.js` | Tables page |
| `src/app-quiz.js` | Question generator and quiz runner |
| `src/app-pyq.js` | PYQ browser |
| `src/app-content.js` | Learn, Help, the animated explainer, Developed by (team details are at the top) |
| `src/app-report.js` | Report builder and the PDF, Word and text writers |
| `src/app-core.js`, `app-main.js` | Router, storage, theme, saving files |
| `src/styles.css`, `template.html` | Styles (light and dark) and page shell |

The engine files have no DOM code, so the tests run them directly in Node.

## Editing and testing

You need Node 18 or newer. There is nothing to install.

    node build.js           # rebuild dist/index.html after changing anything in src/
    node test/run.js        # 2,885 checks on the relation engine
    node test/tables.js     # 217 checks on the table engine and file writers

`test/run.js` also re-solves every PYQ that has a numeric or computable answer and compares
it with the answer key. `test/tables.js` normalizes each sample table at every stage and
checks that joining the pieces gives back exactly the original rows.

Two browser tests use Python and Playwright (`pip install playwright`, then
`python -m playwright install chromium`):

- `test/browser.py` clicks through every page in both themes and at phone and tablet widths,
  and opens each downloaded file.
- `test/exports_offline.py` blocks the internet libraries and checks that every Tables export
  and the Word report still download.

## Team

| | Reg. no. | Worked on |
|---|---|---|
| Yusuf Khan | 25BCE5307 | Parser, closure and key search; normal-form analysis, dependency diagram and Lab page; table engine (1NF repair, dependency and key discovery, SQL) |
| Mohammed Mubashir Hasan | 25BCE5209 | Minimal cover, 3NF synthesis, BCNF decomposition; chase test and dependency preservation; Tables page, quiz generator and PYQ bank |
| E T Suhas Aradhya | 25BCE5560 | Learn section and animated explainer; Help and report downloads; testing (engine, table and browser suites) |

## References

- A. Silberschatz, H. F. Korth, S. Sudarshan, *Database System Concepts*, 7th ed., ch. 7.
- R. Elmasri, S. B. Navathe, *Fundamentals of Database Systems*, 7th ed., ch. 14–15.
- C. J. Date, R. Fagin, "Simple conditions for guaranteeing higher normal forms in
  relational databases", *ACM TODS* 17(3), 1992.
- Y. Huhtala et al., "TANE: An efficient algorithm for discovering functional and
  approximate dependencies", *The Computer Journal* 42(2), 1999.
- GATE question papers (IISc / IITs). The question texts are paraphrased.
