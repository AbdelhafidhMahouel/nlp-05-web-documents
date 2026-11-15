# nlp-05-web-documents

[![Python 3.14](https://img.shields.io/badge/python-3.14%2B-blue?logo=python)](pyproject.toml)
[![CI](https://github.com/AbdelhafidhMahouel/nlp-05-web-documents/actions/workflows/ci-python-zensical.yml/badge.svg)](https://github.com/AbdelhafidhMahouel/nlp-05-web-documents/actions/workflows/ci-python-zensical.yml)
[![Documentation](https://img.shields.io/badge/docs-site-blue?logo=github)](https://abdelhafidhmahouel.github.io/nlp-05-web-documents/)

[![uv managed](https://img.shields.io/badge/uv-managed-DE5FE9)](https://docs.astral.sh/uv/)
[![ty type checked](https://img.shields.io/badge/ty-type_checked-2F80ED)](https://docs.astral.sh/ty/)
[![Zensical docs](https://img.shields.io/badge/Zensical-docs-purple)](https://zensical.org/)
[![MIT](https://img.shields.io/badge/license-MIT-yellow.svg)](LICENSE)

> Acquire, validate, and transform HTML web documents using Python.

## Project Overview

Author: Abdelhafidh Mahouel

Date: 2026 November

Fifth project in the Web Mining and Applied NLP course
(44620, Northwest Missouri State University).

This repository was created from the
[nlp-05-web-documents example project](https://github.com/denisecase/nlp-05-web-documents)
by Denise Case and customized for my own work.

This project introduces web document acquisition
and structured information extraction from HTML.

The goal is to retrieve a web page,
validate its document structure,
extract meaningful text and metadata,
and save the results for analysis.

The project extends the EVTL pipeline approach
introduced in Module 4 to work with HTML documents
rather than JSON API responses.

The project is configured in
[pyproject.toml](pyproject.toml).

## Data

The example retrieves an academic paper's abstract page
from [arXiv](https://arxiv.org/abs/2602.20021).

The source document contains information such as:

- Paper title.
- Authors.
- Abstract.
- Subject categories.
- Submission information.
- arXiv identifier.

The pipeline preserves the original HTML
and produces a structured CSV dataset
containing extracted metadata.

## Approach

The project uses a modular Python pipeline
to acquire, validate, transform, and save HTML data.

The pipeline has four stages:

- **Extract** - retrieve HTML from a web page.
- **Validate** - verify that required HTML elements exist.
- **Transform** - extract text and metadata into a structured dataset.
- **Load** - save the processed data to a CSV file.

The example uses Requests for HTTP acquisition,
BeautifulSoup for HTML parsing,
and Pandas for tabular data processing.

The Transform stage also calculates
derived fields such as abstract word count
and number of authors.

Explore these important project components:

- [pyproject.toml](pyproject.toml) - project identity, dependencies, and tool configuration.
- [src/nlp/pipeline_web_html.py](src/nlp/pipeline_web_html.py) - main EVTL pipeline orchestrator.
- [src/nlp/config.py](src/nlp/config.py) - example web page and path configuration.
- [src/nlp/stage01_extract.py](src/nlp/stage01_extract.py) - retrieve HTML.
- [src/nlp/stage02_validate.py](src/nlp/stage02_validate.py) - validate HTML structure.
- [src/nlp/stage03_transform.py](src/nlp/stage03_transform.py) - extract metadata and build a DataFrame.
- [src/nlp/stage04_load.py](src/nlp/stage04_load.py) - save processed results.

My custom Phase 5 files (the original example files are kept as working references):

- [src/nlp/pipeline_web_html_mahouel.py](src/nlp/pipeline_web_html_mahouel.py) - custom EVTL pipeline orchestrator.
- [src/nlp/config_mahouel.py](src/nlp/config_mahouel.py) - custom web page and output paths.
- [src/nlp/stage02_validate_mahouel.py](src/nlp/stage02_validate_mahouel.py) - custom validation checks.
- [src/nlp/stage03_transform_mahouel.py](src/nlp/stage03_transform_mahouel.py) - custom extraction and continuous improvement analysis.
- [tests/](tests/) - automated tests.
- [.github/workflows/](.github/workflows/) - automated testing and documentation deployment.
- [docs/](docs/) - project documentation.
- [zensical.toml](zensical.toml) - documentation configuration.

The goal is to understand how information
is extracted from semi-structured web documents
and transformed into analysis-ready data.

## My Changes

### Phase 4: Technical Modification

I added two derived fields to the Transform stage in
[src/nlp/stage03_transform.py](src/nlp/stage03_transform.py):

| Field | How it is calculated | What it measures |
| --- | --- | --- |
| `abstract_sentence_count` | Splits the abstract with a regular expression after `.`, `!`, or `?` followed by whitespace, then counts the pieces. | Number of sentences in the abstract. |
| `avg_words_per_sentence` | Abstract word count divided by sentence count, rounded to 1 decimal (returns 0.0 if no sentences). | A simple readability measure: longer sentences are usually harder to read. |

Design notes:

- The regex only splits when whitespace follows the punctuation, so decimals like `3.5` stay together.
- A guard prevents division by zero when the abstract is missing.
- Both values are logged and saved as new columns in the CSV.

Results for the example paper (arXiv 2602.20021, "Agents of Chaos"):

| abstract_word_count | abstract_sentence_count | avg_words_per_sentence |
| --- | --- | --- |
| 177 | 9 | 19.7 |

### Phase 5: Apply the Skills to a New Problem (Manufacturing / Continuous Improvement)

I applied the EVTL pipeline to a manufacturing process engineering paper:
[A Mixed-Integer Linear Programming (MILP) for Garment Line Balancing](https://arxiv.org/abs/2502.17508)
(arXiv 2502.17508).

I created separate custom files so the original example pipeline
(and my Phase 4 work) still runs unchanged.

**Custom configuration** - [config_mahouel.py](src/nlp/config_mahouel.py)

- `PAGE_URL` points to the new paper.
- Outputs are saved as `mahouel_raw.html` and `mahouel_processed.csv`.

**Custom validation** - [stage02_validate_mahouel.py](src/nlp/stage02_validate_mahouel.py)

- Keeps the required checks (title, authors, abstract, subjects, dateline).
- NEW: confirms the canonical link exists (needed for the arXiv ID).
- NEW: warns if the abstract has fewer than 50 words.

**Custom transformation** - [stage03_transform_mahouel.py](src/nlp/stage03_transform_mahouel.py)

- Keeps the Phase 4 fields (sentence count and average words per sentence).
- NEW `ci_keyword_hits`: total count of 20 continuous improvement and
  process engineering terms (such as lean, kaizen, line balancing,
  cycle time, bottleneck, throughput, waste) in the title and abstract.
- NEW `ci_keywords_found`: which terms were found and how many times.
- NEW `subjects_full`: the full subject list with arXiv codes.
- Matching is case-insensitive and uses word boundaries,
  so `lean` does not match inside `clean`.

**Custom pipeline** - [pipeline_web_html_mahouel.py](src/nlp/pipeline_web_html_mahouel.py)

- Imports the custom config, validate, and transform stages.
- Reuses the original Extract and Load stages.

Results for the custom paper:

| Field | Value |
| --- | --- |
| Title | A Mixed-Integer Linear Programming (MILP) for Garment Line Balancing |
| Authors | Ray Wai Man Kong, Ding Ning, Theodore Ho Tin Kong |
| Author count | 3 |
| Submitted | 22 Feb 2025 |
| Primary subject | Mathematics > Optimization and Control |
| Full subjects | Optimization and Control (math.OC); Computational Engineering, Finance, and Science (cs.CE) |
| Abstract word count | 159 |
| Abstract sentence count | 7 |
| Avg words per sentence | 22.7 |
| CI keyword hits | 3 |
| CI keywords found | lean (1); efficiency (1); line balancing (1) |

**Interpretation:** Although the paper solves a classic manufacturing
problem, it uses optimization vocabulary rather than shop-floor
continuous improvement terms, so only 3 CI keywords were found.
arXiv also classifies it under mathematics, not manufacturing.
A future improvement would add operations research terms such as
optimization, scheduling, assignment, and production planning
to the keyword list.

## Run the Project

Follow the [Professional Python Workflow Guide](https://denisecase.github.io/pro-analytics-02/)
to set up your machine and project environment.

From a VS Code terminal in the project root:

Run the original example pipeline (includes the Phase 4 modification):

```shell
uv run python -m nlp.pipeline_web_html
```

Run my custom Phase 5 pipeline (manufacturing paper):

```shell
uv run python -m nlp.pipeline_web_html_mahouel
```

**Windows note:** If Windows Application Control blocks the Python
launcher (os error 4551), run this first in each new terminal:

```shell
$env:UV_PYTHON_PREFERENCE = "only-managed"
```

## Results

The pipeline retrieves an HTML document,
validates its structure,
extracts bibliographic information,
and writes the results to a CSV file.

The resulting dataset includes text and metadata
along with derived measures
such as abstract word count and author count.

Successful execution produces:

```shell
========================
Pipeline executed successfully!
========================
```

The following files are created:

- `project.log` - pipeline execution log.
- `data/raw/case_raw.html` - original HTML document.
- `data/processed/case_processed.csv` - extracted and transformed data.

The custom Phase 5 pipeline creates:

- `data/raw/mahouel_raw.html` - original HTML of the manufacturing paper.
- `data/processed/mahouel_processed.csv` - extracted data with continuous improvement fields.

These artifacts provide evidence of successful
HTML acquisition, validation, transformation, and persistence.

## Verification

Run the automated tests:

```shell
uv run python -m pytest
```

## Project Documentation

The [hosted documentation site](https://abdelhafidhmahouel.github.io/nlp-05-web-documents/)
is published using Zensical and GitHub Pages.

The documentation site is used to present the project report,
covering such sections as data, methods, results, visualizations,
interpretation, and conclusions.

## CI/CD and GitHub Actions

This project includes continuous integration and continuous deployment (CI/CD)
to check project files and publish the docs.

GitHub Actions include:

- [CI: Python and Documentation Checks](https://github.com/AbdelhafidhMahouel/nlp-05-web-documents/actions/workflows/ci-python-zensical.yml)
- [CD: Documentation Deployment](https://github.com/AbdelhafidhMahouel/nlp-05-web-documents/actions/workflows/deploy-zensical.yml)

## Developer Reference

Follow the
[Professional Python Workflow Guide](https://denisecase.github.io/pro-analytics-02/)
for **detailed instructions and troubleshooting**.

### Initialize and Update the Python Environment

With the project open in VS Code, open a VS Code terminal.
Paste each command and hit Enter or Return after to run it.

```shell
uvx pup-clean --delete
uv self update
uv python pin 3.14
uv python install
uv lock --upgrade
uv sync
uv audit
```

### Set Up and Run Git Hooks

Set up and run the git hooks to perform
basic checks automatically before
any changes get pushed to GitHub.

In the VS Code terminal,
paste each command and hit Enter or Return after to run it.

```shell
uvx prek install --force
uvx prek update --freeze --cooldown-days 7

git add -A
uvx prek run --all-files
# repeat if changes were made
uvx prek run --all-files
```

### Run Common Chores

Run linters, formatters, type checks, tests, and
build the documentation.

```shell
uv run ruff check . --fix
uv run ruff format .

uv run ty check
uv run python -m pytest
uv run python -m zensical build
```

### Git add-commit-push to GitHub

After making useful changes, save your work to GitHub.

```shell
git add -A
git commit -m "describe your changes in quotes"
git push -u origin main
```

## Developer Notes

- Use the **UP ARROW** and **DOWN ARROW** in the terminal to scroll through past commands.
- Use `CTRL+f` to find (and replace) text within a file.

## Developer Tools

This project uses professional Python tools to support development,
testing, documentation, and code quality.

- [uv](https://docs.astral.sh/uv/) - Python environments and dependencies.
- [Ruff](https://docs.astral.sh/ruff/) - Python linting and formatting, configured in [.ruff.toml](.ruff.toml).
- [ty](https://docs.astral.sh/ty/) - Python type checking.
- [pytest](https://docs.pytest.org/) - automated Python testing, configured in [.pytest.toml](.pytest.toml).
- [prek](https://prek.j178.dev/) - Git hooks and automated checks, configured in [prek.toml](prek.toml).
- [Zensical](https://zensical.org/) - project documentation, configured in [zensical.toml](zensical.toml).

## Links

- [Hosted Documentation](https://abdelhafidhmahouel.github.io/nlp-05-web-documents/)
- [Professional Python Workflow Guide](https://denisecase.github.io/pro-analytics-02/)
- [Citation](CITATION.cff)
- [License](LICENSE)
