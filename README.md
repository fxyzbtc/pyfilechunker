# PyFileChunker

[![PyPI version](https://badge.fury.io/py/pyfilechunker.svg)](https://badge.fury.io/py/pyfilechunker) <!-- Placeholder: Add actual PyPI badge once published -->
<!-- [![Build Status](https://travis-ci.org/fxyzbtc/pyfilechunker.svg?branch=main)](https://travis-ci.org/fxyzbtc/pyfilechunker) --> <!-- Placeholder: Add CI badge if applicable -->

A Python utility to intelligently split large files into smaller, manageable chunks based on user-defined record boundaries, not just arbitrary sizes or line counts.

## Pains

Working with extremely large text-based files (e.g., logs, data dumps, XML/JSON streams) can be challenging:

*   **Memory Issues:** Loading the entire file into memory is often impossible.
*   **Processing Bottlenecks:** Processing the file sequentially can be slow.
*   **Incomplete Records:** Simple splitting by size or line count can break records mid-way, corrupting data or making parsing difficult.

`PyFileChunker` addresses these pains by allowing you to split files *between* records, ensuring each chunk contains complete records. It uses memory mapping (`mmap`) for efficiency when searching for boundaries.

## Features

*   **Record Boundary Splitting:** Define custom start and end markers for your records (e.g., `<RECORD>`, `</RECORD>`, `BEGIN TRANSACTION`, `END TRANSACTION`).
*   **Intelligent Boundary Finding:** Attempts to find the nearest record boundaries around the ideal chunk split points.
*   **Memory Efficient:** Uses `mmap` to avoid loading the entire file into memory when locating split points.
*   **Configurable:** Control the desired number of chunks and the record markers.
*   **Command-Line Interface:** Easy-to-use CLI powered by Typer.
*   **Python Module:** Can be imported and used directly in your Python scripts.

## Installation

**From PyPI (Recommended):**

```bash
pip install pyfilechunker
```

**From Source:**

1.  Clone the repository:
    ```bash
    git clone https://github.com/fxyzbtc/pyfilechunker.git # Replace with your actual repo URL
    cd pyfilechunker
    ```
2.  Install using pip:
    ```bash
    pip install .
    ```
    For development, install in editable mode with development dependencies:
    ```bash
    pip install -e .[dev]
    ```

## Usage

You can use `PyFileChunker` in three ways:

**1. As an Installed Script (`filechunker`)**

This is the most common way after installing via pip.

```bash
filechunker [OPTIONS] FILE_PATH
```

**Arguments:**

*   `FILE_PATH`: The path to the large file you want to chunk. [required]

**Options:**

*   `--num-chunks INTEGER`: The desired number of chunks. [default: 5]
*   `--record-begin TEXT`: String marking the beginning of a record. [default: `<SUBBEGIN>`]
*   `--record-end TEXT`: String marking the end of a record. [default: `<SUBEND>`]
*   `--output-dir DIRECTORY`: The directory to save the chunk files. [default: . (current directory)]
*   `--help`: Show the help message and exit.

**Example:**

Split `my_large_log.log` into approximately 10 chunks, using `START` and `END` as record markers, saving chunks to the `output_chunks/` directory:

```bash
filechunker my_large_log.log --num-chunks 10 --record-begin "START" --record-end "END" --output-dir output_chunks/
```

**2. As a Python Module (`python -m pyfilechunker`)**

You can run the module directly using Python's `-m` flag. This is useful if the script isn't in your PATH or you prefer this invocation. The arguments and options are the same as the script.

```bash
python -m pyfilechunker [OPTIONS] FILE_PATH
```

**Example:**

```bash
python -m pyfilechunker data.xml --num-chunks 20 --record-begin "<item>" --record-end "</item>" --output-dir ./chunks
```

**3. Importing in Python Code**

You can import and use the `chunk_it` function directly in your Python scripts for more complex workflows.

```python
from pyfilechunker import chunk_it
from pathlib import Path

input_file = "path/to/your/large_file.txt"
output_directory = "chunk_output"
num_chunks_desired = 15
start_marker = "BEGIN_RECORD"
end_marker = "END_RECORD"

try:
    # Ensure output directory exists
    Path(output_directory).mkdir(parents=True, exist_ok=True)

    created_files = chunk_it(
        filename=input_file,
        num_chunks=num_chunks_desired,
        record_begin=start_marker,
        record_end=end_marker,
        output_dir=output_directory
    )

    if created_files:
        print(f"Successfully created {len(created_files)} chunks in '{output_directory}':")
        for f in created_files:
            print(f"- {f}")
    else:
        print("No chunk files were created.")

except FileNotFoundError:
    print(f"Error: Input file not found at {input_file}")
except Exception as e:
    print(f"An error occurred: {e}")

```

## Development Guide

**Prerequisites:**

*   Python >= 3.12
*   Git
*   `pip` and `venv` (recommended)

**Setup:**

1.  **Clone:** `git clone https://github.com/fxyzbtc/pyfilechunker.git && cd pyfilechunker`
2.  **Create Virtual Environment:** `python -m venv .venv`
3.  **Activate Environment:**
    *   Windows: `.venv\Scripts\activate`
    *   macOS/Linux: `source .venv/bin/activate`
4.  **Install Dependencies:** `pip install -e .[dev]` (This installs the package in editable mode along with `pytest`)

**Running Tests:**

Make sure your virtual environment is activated.

```bash
pytest
```

**Building the Package:**

Ensure you have the build tools installed:

```bash
pip install build
```

Then run the build command:

```bash
python -m build
```

This will create distribution files (wheel and sdist) in the `dist/` directory.

**Contributing:**

Contributions are welcome! Please feel free to open an issue or submit a pull request.
(Add more specific contribution guidelines if desired, e.g., code style, PR process).

## License

<!-- Specify your license here, e.g., MIT License. If you haven't chosen one, consider adding one. -->
This project is licensed under the MIT License. (Update if needed)

