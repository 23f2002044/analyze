# Project Overview

This repository demonstrates a data processing workflow using Python with Pandas, linted with Ruff, and deployed via GitHub Actions. It includes an example of a single-file responsive HTML application built with Tailwind CSS.

## Table of Contents

- [Project Overview](#project-overview)
- [Files in this Repository](#files-in-this-repository)
- [Setup and Local Execution](#setup-and-local-execution)
  - [Prerequisites](#prerequisites)
  - [Running the Data Processing Script](#running-the-data-processing-script)
- [Data Conversion](#data-conversion)
- [GitHub Actions Workflow](#github-actions-workflow)
  - [Linter (Ruff)](#linter-ruff)
  - [Data Processing and Output](#data-processing-and-output)
  - [GitHub Pages Deployment](#github-pages-deployment)
- [Responsive HTML App](#responsive-html-app)
- [License](#license)

## Files in this Repository

- `index.html`: A single-file responsive HTML application using Tailwind CSS.
- `execute.py`: A Python script to process `data.csv` and output `result.json`.
- `data.csv`: The converted data file from `data.xlsx` for processing.
- `.github/workflows/ci.yml`: GitHub Actions workflow for CI/CD.
- `README.md`: This documentation file.
- `LICENSE`: The MIT License.

## Setup and Local Execution

### Prerequisites

Ensure you have Python 3.11+ and pip installed.

```bash
python --version
pip --version
```

Install the required Python packages:

```bash
pip install pandas ruff openpyxl
```

### Running the Data Processing Script

The `execute.py` script processes the `data.csv` file, aggregates data, and outputs the result to `result.json`.

**The Nontrivial Error and Its Fix in `execute.py`**:
Initially, the script might have faced issues with data types in the `value` column. While `pd.read_csv` often infers types correctly, external data sources can sometimes lead to columns being read as `object` (string) type, especially if there are mixed types or unexpected non-numeric entries.
The non-trivial error was the potential `TypeError` or incorrect aggregation if the `value` column was not strictly numeric.
The fix implemented in `execute.py` is to explicitly convert the `value` column to a numeric type using `pd.to_numeric(df['value'], errors='coerce')`. The `errors='coerce'` argument ensures that any non-numeric values are converted into `NaN` (Not a Number), preventing the script from crashing. Subsequently, `df.dropna(subset=['value'], inplace=True)` removes rows where the `value` could not be converted, ensuring that all aggregations are performed only on valid numeric data. This makes the script more robust against messy real-world data.

To run the script locally and generate `result.json`:

```bash
python execute.py > result.json
```

This command will execute the script and redirect its standard output to `result.json`.

## Data Conversion

The original `data.xlsx` file has been converted into `data.csv` for easier, consistent processing across environments and for version control. `data.csv` is committed directly to this repository.

## GitHub Actions Workflow

A GitHub Actions workflow (`.github/workflows/ci.yml`) is configured to automate the CI/CD process on every `push` and `pull_request` to the `main` branch.

### Linter (Ruff)

The workflow includes a step to run `ruff`, a highly performant Python linter. It checks for code style compliance and potential errors, ensuring code quality and consistency. The results are visible in the CI logs.

```yaml
- name: Run Ruff Linter
  run: |
    ruff check .
    ruff format . --check
```

### Data Processing and Output

The core of the CI is executing `execute.py` and generating `result.json`.

```yaml
- name: Execute script and generate result.json
  run: python execute.py > result.json
```

This step ensures that `result.json` is always up-to-date with the latest `data.csv` and `execute.py` logic, without being committed to the repository.

### GitHub Pages Deployment

The generated `result.json` is published via GitHub Pages. The workflow first creates a `gh-pages` directory, moves `result.json` into it, and then creates a simple `index.html` within `gh-pages` to facilitate accessing the JSON output via the generated GitHub Pages URL.

To view the deployed result, check the GitHub Pages URL associated with your repository. The `result.json` can be accessed directly at `https://<YOUR_USERNAME>.github.io/<YOUR_REPO_NAME>/result.json` (or via the link on the GitHub Pages site).

## Responsive HTML App

The `index.html` file provides a simple, responsive web page styled with Tailwind CSS. It's a standalone client-side application that demonstrates modern web development practices. You can open `index.html` directly in your browser.

## License

This project is licensed under the MIT License - see the `LICENSE` file for details.