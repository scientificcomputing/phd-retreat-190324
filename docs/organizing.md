---
theme: default
paginate: true
header: 'Open Science and reproducibility'
footer: 'Organizing your projects - 19.03.24 - Henrik Finsberg'
size: 16:9
style: |
  .small-text {
    font-size: 0.55rem;
  }
html: true
marp: true
---

# Organizing your projects

- `git` version control system
- Creating a folder structure
- GitHub for backup and collaboration
- Licenses
- Data



---

## Why version control systems?

<!--
Ask the student if they are familiar with git
See if some of them already have an idea on what are the
benefits of using version control
-->

* To keep a history of what has been changed and why
* To make it easy to go back to a previous version
* To make changes while maintaining a working version

![bg fit right](figures/project-history.svg)
<p class="small-text">Image is used under a CC-BY 4.0 license. DOI: 10.5281/zenodo.3332807.</p>

---

![w:700 center](figures/git_stage_commit.svg)
<p class="small-text">Image is used under a CC-BY 4.0 license. https://coderefinery.github.io/git-intro/basics/.</p>

---

---

## Resources for learning about git

<!--
Here are some resources, also with links to additional resources.
For now I will assume a very basic understanding of git
-->

- https://coderefinery.github.io/git-intro/
- https://the-turing-way.netlify.app/reproducible-research/vcs/vcs-resources#further-reading


---


## Setting up your first project

<!--
When setting up your first project it is nice to have a template for which files that should be part of the project
We have created two template repos for this which will basically copy all the files from that repo into your own repo

Cookiecutter is another alternative where you run a command an it will prompt you with some questions an fill it in

Note! These examples are very python centric. How many are not primarily using python
-->

- Templates
    - Copy all files from an existing repository
    - https://github.com/scientificcomputing/example-paper
    - https://github.com/scientificcomputing/example-paper-fenics

- Cookiecutters
    - Script that generate the files based on a few questions
    - https://github.com/scientificcomputing/generate-paper
    - https://github.com/scientific-python/cookie
    - https://www.cookiecutter.io/templates

---


## Demo cookiecutter


```
python3 -m pip install cookiecutter
python3 -m cookiecutter gh:scientificcomputing/generate-paper
```
(Here you can also use [pipx](https://pypa.github.io/pipx/))

---

```
research_paper_1
├── CITATION.cff        # Info about how to cite your project
├── LICENSE             # The license
├── README.md           # What the user should read first
├── _config.yml         # Configurations for docs
├── _toc.yml            # Table of contents for docs
├── code                # Where to put your code
│   └── README.md       # Description of the code
├── cspell.config.yaml  # Dictionary for spell checker
├── data                # Where to put your data
│   └── README.md       # Description of the data
├── docker
│   └── Dockerfile      # The docker file
├── docs                # Where to put your docs
│   ├── logo.png        # Simula Logo to put in documentation
│   └── references.bib  # Where to put your references
├── environment.yml     # Conda dependencies
└── pyproject.toml      # Python metadata and dependencies
```

---

## What is GitHub?

* A place to host your remote repositories
* To make it easier to collaborate with others
* To keep a backup (on GitHub)
* Create repositories under your group's GitHub organization (this will make it easier if you are unavailable)
    - https://github.com/scientificcomputing
    - https://github.com/ComputationalPhysiology

---

## Code review process

* Open issue describing the problem. Use the issue tracker to discuss whether this needs to be solved.
* Clone (or fork) the repository
* Create a new branch (e.g `git checkout -b finsberg/fix-typo-in-readme`)
* Push your changes to this new branch
* Create a pull request, refer to the issue and continue the discussion

---


## Setting up your first project

- Templates
    - Copy all files from an existing repository
    - https://github.com/scientificcomputing/example-paper
    - https://github.com/scientificcomputing/example-paper-fenics

- Cookiecutters
    - Script that generate the files based on a few questions
    - https://github.com/scientificcomputing/generate-paper
    - https://github.com/scientific-python/cookie
    - https://www.cookiecutter.io/templates

---
