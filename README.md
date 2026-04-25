# Notebook 2019

A collection of coursework, study notes, and programming exercises from 2019, covering bioinformatics, machine learning, and statistical computing.

## Repository Structure

```
.
├── Notes/                          # Standalone study guides and tutorials
│   ├── Bioinformatics_Introduction.md
│   ├── GitHub_Usage.md
│   └── Jupyter_Installation.md
├── Homework/                       # University coursework assignments
│   ├── Bioinformatics_CAU/         # Sequence alignment with BLOSUM62 scoring
│   ├── C_CAU/                      # C programming exercises by topic
│   ├── Python_CAU/                 # Python basics practice problems
│   ├── Python_PKU/                 # Web crawler + word cloud project
│   └── R_CAU/                      # R statistics lab notes
├── Python/                         # Self-study Python projects
│   ├── MachineLearning_Ng/         # Andrew Ng ML course notes & exercises
│   ├── PythonCrawler_Douban/       # Douban Top250 movie data crawler
│   └── PythonFromStatistician_Book/ # Book notes: NumPy, Pandas, Matplotlib, SciPy
├── R/                              # Self-study R projects
│   └── Examples_CAU/               # R programming course examples
├── .gitignore
├── LICENSE                         # MIT License
└── README.md
```

## Homework

| Folder | Course | Description |
|---|---|---|
| `Bioinformatics_CAU/` | Bioinformatics (CAU) | Python implementation of sequence alignment with BLOSUM62 scoring matrix backtracking. Includes a STAR aligner configuration file (`mapping.gtex`). |
| `C_CAU/` | C Programming (CAU) | Basic C exercises organized by topic: input/output, sequential/loop/array structures, and functions. |
| `Python_CAU/` | Python Basics (CAU) | Fundamentals: string manipulation, palindrome detection, Hanoi towers, prime numbers, and more. |
| `Python_PKU/` | Python (PKU) | Web crawler fetching Sohu News articles, with Chinese word segmentation (jieba) and word cloud visualization. |
| `R_CAU/` | R Statistics (CAU) | Lab notes covering ANOVA, multicollinearity, linear regression, cluster analysis, and ggplot2. |

## Python

| Folder | Description |
|---|---|
| `MachineLearning_Ng/` | Notes from Andrew Ng's Machine Learning course. Covers algorithm fundamentals, mathematical derivations (with LaTeX), and Python implementations of exercises (linear/logistic regression, neural networks). |
| `PythonCrawler_Douban/` | Jupyter notebook implementing a static web crawler using Requests + BeautifulSoup to scrape Douban Top250 movie data. |
| `PythonFromStatistician_Book/` | Study notes from *Python: A Statistician's Perspective* by Wu Xizhi. Covers NumPy, Pandas, Matplotlib, SciPy, basic data processing, and hypothesis testing. |

## R

| Folder | Description |
|---|---|
| `Examples_CAU/` | Code examples from the *R Programming* course at CAU. Topics include dplyr, tidyr, stringr, purrr, ggplot2 (facets, colors, points, boxplots, bar charts, histograms), cowplot, rvest web scraping, GIS with leaflet, formattable, and Monty Hall simulation. |

## Notes

| File | Description |
|---|---|
| `Bioinformatics_Introduction.md` | Introduction to sequencing platforms (Illumina, Roche 454, SOLiD, PacBio, Ion), genome assembly tools (SSAKE, Edena, SOAPdenovo), and ChIP-seq data analysis. |
| `GitHub_Usage.md` | Git and GitHub tutorial notes covering SSH setup, branching, merging, rebasing, remote operations, issues, and pull requests. |
| `Jupyter_Installation.md` | Step-by-step Jupyter Notebook installation guide for both Windows (Anaconda) and Linux (remote server with remote access). |

## License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.
