---
theme: default
paginate: true
header: 'Open Science and reproducibility'
footer: 'Recording computational steps - 19.03.24 - Henrik Finsberg'
size: 16:9
style: |
  .small-text {
    font-size: 0.55rem;
  }
html: true
marp: true
---

# Recording computational steps

---

## Analysis can usually be divided into steps

1. Preprocessing of data
    - Getting data into the right format
    - Creating meshes


2. Running the main analysis
    - Running simulations
    - Training a model

---

3. Postprocessing
    - Extracting results
    - Creating tables
    - Creating figures

* You should make sure that these steps are documented well and preferably automated

---

## Example workflow

```
├── LICENSE
├── README.md
├── code
│   ├── run-preprocess.py
│   ├── run-analysis.py
│   └── run-postprocess.py
├── raw_data
│   └── raw_data.txt
└── requirements.txt
```
```
python3 code/run-preprocess.py raw_data/raw_data.txt -o data
python3 code/run-analysis.py data -o results
python3 code/run-postprocess results -o figures
```

---

## Creating scripts that can take command line arguments

It is a good idea to make it possible to pass arguments

- Makes it easy to change directories
- Makes it easy to run on clusters where the paths might not be relative

---

## Tools to create automated workflows

**bash**
Create script balled `run_all_experiments.sh`
```bash
for file in file1 file2; do
    python3 code/run-preprocess.py raw_data/${file}.txt -o data/${file}.csv
    python3 code/run-analysis.py data/${file}.csv -o results/${file}.csv
    python3 code/run-postprocess results/${file}.csv -o figures/${file}.png
done
```

---

## Tools to create automated workflows

**python**
Create script balled `run_all_experiments.py`
```python
import subprocess as sp

for file in ["file1", "file2"]:
    sp.run(["python3", "code/run-preprocess.py", f"raw_data/{file}.txt", "-o",  f"data/{file}.csv"])
    sp.run(["python3", "code/run-analysis.py", f"data/{file}.csv", "-o", "results/{file}.csv"])
    sp.run(["python3", "code/run-postprocess", f"results/{file}.csv", "-o", "figures/{file}.png"])
```

---

## Other workflow tools

- `make` using [`Makefile`](https://makefiletutorial.com)
- [Snakemake](https://snakemake.readthedocs.io/en/stable/tutorial/tutorial.html)
    - also checkout https://coderefinery.github.io/reproducible-research/workflow-management/

---

## Exercise:

Visit: https://github.com/finsberg/word-count

- Create a new repository using this template (i.e click `Use this template`)
- Clone the repository locally

```
├── LICENSE
├── README.md
├── code
│   ├── count.py
│   └── plot.py
├── data
│   ├── LICENSE_TEXTS.md
│   ├── abyss.txt
│   ├── isles.txt
│   ├── last.txt
│   └── sierra.txt
├── environment.yml
├── requirements.in
└── requirements.txt
```

---

- Create an environment and install the dependencies
    - Instructions are written in the README file

- Do Exercise 1


---

## Verifying computations using tests

It is essential that we know that our program is working correctly

```python
# src/code.py

def find_first_word_that_starts_with(wordlist: list[str], starts_with: str) -> str | None:

    for word in wordlist:
        if word.startswith(starts_with):
            return word
    return None
```

```python
# tests/test_code.py

def test_find_first_word_that_starts_with():
    lst = ["This is a test"]
    assert find_first_word_that_starts_with(lst, "t") == "Test"
```

---

## Use `pytest` to run tests in python

Install pytest
```
python3 -m pip install pytest
```

Run pytest
```
python3 -m pytest
```
This will search for all files starting or ending with `test` and run all functions in those files that starts with `test`


---

## Exercise 2:

Add tests to the word count example.

Create a folder called `tests` and a files inside it called `test_word_count`


---

## Hard to write tests?

If you find it hard to write tests, if might be a sign that you should refactor your code. It is a good habit to write the tests before writing the actual code. That forces you to think about how your code can be used from the "outside". This is called test-driven development (TDD)

---

## How to write a test for this code?

```python
def run_analysis():

    data = "data/data.txt"
    ...
    # Do a lof of analysis which generates a numpy array results
    ...
    numpy.save("results.npy", results)
```

Note: You are confident that the results are correct so we just want to make sure that if we change any code in the analysis (or something else), then the results remains the same

---

## Poor mans tests

1. Run the analysis once. This will create a file `results.npy`
2. Change name of that file from `results.npy` to `expected_results.npy`
3. Write the test
    ```python
    def test_run_analysis():
        expected_results = numpy.load("expected_results.npy")
        run_analysis()
        results = numpy.load("results.npy")
        assert results == expected_results

But ideally you should split the code into smaller functions and tests them one by one.
