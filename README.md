# iir — Machine Learning / Natural Language Processing / Information Retrieval

## Overview
This repository collects small experiments and utilities across Machine Learning, Natural Language Processing (NLP), and Information Retrieval (IR). It contains:

- Data preparation utilities (e.g., corpus extraction, CLUTO export, libSVM generation)
- Topic modeling experiments (several LDA/HDPLDA Python scripts)
- Content extraction (Python)
- Language detection (Ruby, with a bundled model)
- Clustering (Ruby HAC variants)
- Miscellaneous neural network and regression experiments (Ruby, R)

The codebase is heterogeneous by design and organized into self-contained folders.

## Project structure
- data/
  - gen_corpus.rb — build a tokenized corpus from raw texts (e.g., Project Gutenberg)
  - gen_cluto.rb — export a CLUTO-compatible sparse matrix from a saved corpus
  - gen_libsvm.rb — generate libSVM-format data from two labeled sources
  - 4million.corpus, ohenry.corpus — example corpus artifacts
  - henry-poe — example libSVM-format lines
- extractcontent/ — Python scripts for training, testing, and web extraction
  - train.py, test.py, webextract.py
- hac/ — Ruby implementations of hierarchical agglomerative clustering (HAC)
  - hac.rb, naive_hac.rb, fselect.rb
- irt/ — Ruby IRT utilities
- langdetect/ — Ruby language detection (train/test/crawl), with model.json
- lda/ — Topic modeling experiments (Python and R)
  - lda.py, lda_cvb0.py, hdp_online.py, hdplda.py, hdplda2.py, llda.py, llda_nltk.py, vocabulary.py, tests and datasets helpers
  - lda.r — R-based LDA experiment
- lib/
  - extract_gutenberg.rb — Project Gutenberg text cleaner
  - infinitive.rb, inflist.txt, wordbook.txt — English lemmatization helpers
- lr/ — Linear/logistic regression experiments (R)
- misc/ — Miscellaneous (e.g., Zipf’s law script, linear regression assets)
- neural/ — Ruby neural network demos (classification, curve fitting, iris, mnist)

## Data utilities

### 1) Build a tokenized corpus (Ruby, Project Gutenberg)
Script: data/gen_corpus.rb

- Cleans raw texts using lib/extract_gutenberg.rb
- Lemmatizes words with lib/infinitive.rb
- Splits large texts into sections, filters short sections, and counts term frequencies
- Saves a PStore file named corpus with:
  - :docs — metadata (e.g., title, number of words)
  - :terms — term → {doc_id → count} postings

Example usage (from the file header):
```
ruby gen_corpus.rb ohenry/1444.zip ohenry/1646.zip ohenry/1725.zip ohenry/2777.zip ohenry/2776.zip
```

Outputs:
- output/<file>.org — original extracted text
- output/<file> — cleaned text after Gutenberg extraction
- corpus — PStore database with docs and terms

Notes:
- The script auto-unzips “.zip” inputs and extracts “*.txt” inside each archive.
- The first non-empty line of a section is used as its title; sections with fewer than 1,000 tokens are skipped.

### 2) Export CLUTO inputs from a saved corpus
Script: data/gen_cluto.rb

- Reads a PStore “corpus” created by gen_corpus.rb
- Produces:
  - <corpus>.mat — CLUTO sparse matrix (docs × terms)
  - <corpus>.clabel — term labels (one per line)
  - <corpus>.rlabel — row labels (document titles)

Example:
```
ruby gen_cluto.rb corpus
```
If the input has an extension, the output filename base is the input name without its last extension.

### 3) Generate libSVM-format data from two sources
Script: data/gen_libsvm.rb

- Cleans and lemmatizes two input texts (positive and negative)
- Splits long texts into fixed-size slices and builds a global vocabulary
- Emits libSVM lines with +1/-1 labels

Usage:
```
ruby gen_libsvm.rb [positive] [negative]
```

Example output (see data/henry-poe):
```
+1 9:1 208:1 255:1 276:3 300:1 ...
```

## Topic modeling (lda/)
This folder contains several Python and R scripts exploring topic models:
- Variants include collapsed variational Bayes (lda_cvb0.py), online HDP-LDA (hdp_online.py), hierarchical DP LDA (hdplda.py, hdplda2.py), labeled LDA (llda.py, llda_nltk.py), and utilities like vocabulary.py.
- Test and dataset helpers are included (e.g., lda_test.py, lda_test2.py, test_hdplda2.py, twentygroups.py).

Inputs, parameters, and execution for each script vary; consult the source files for details before running.

## Content extraction (extractcontent/)
Python scripts for training/testing content extraction and extracting from the web:
- train.py — training utility
- test.py — evaluation utility
- webextract.py — web extraction helper

Refer to the script docstrings and arguments for usage.

## Language detection (langdetect/)
Ruby language detection utilities:
- train.rb — model training
- detect.rb — runtime detection
- crawler.rb — data collection
- model.json — bundled detection model
- Additional tests and helpers

Check each file’s usage comments to run training and detection.

## Clustering (hac/)
Ruby implementations of hierarchical agglomerative clustering and utilities:
- hac.rb, naive_hac.rb — clustering algorithms
- fselect.rb — feature selection helper

## Neural and regression experiments
- neural/ — Ruby demos (classification tasks, curve fitting, iris, MNIST)
- lr/ and misc/ — R scripts and small utilities (e.g., linear regression, Zipf’s law)

## Example datasets
- data/ohenry.corpus, data/4million.corpus — example corpus artifacts
- data/henry-poe — example libSVM-format lines (+1/-1 followed by index:value pairs)

## Development notes
- Scripts are language-specific and largely standalone. Use the appropriate interpreter for each file:
  - Ruby for .rb
  - Python for .py
  - R for .r
- Some Ruby utilities depend on project-local helpers under lib/ (e.g., extract_gutenberg.rb and infinitive.rb). Keep relative paths intact when running from within their directories.
- Generated artifacts (e.g., output/ cleaned texts, corpus PStore, CLUTO files, libSVM lines) are written to the working directory or to the script-defined output paths.

## Contributing
- Add new experiments in self-contained folders.
- Prefer small, readable scripts with minimal external dependencies.
- Include minimal usage comments at the top of scripts where applicable.
- Keep data-generating utilities deterministic and document input/output formats.
