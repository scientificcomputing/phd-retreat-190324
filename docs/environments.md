---
theme: default
paginate: true
header: 'Open Science and reproducibility'
footer: 'Recording dependencies and environment - 19.03.24 - Henrik Finsberg'
size: 16:9
style: |
  .small-text {
    font-size: 0.55rem;
  }
html: true
marp: true
---

# Recording dependencies and environment


---

## What is an environment?

* An environment consists of the operating system, installed packages (with specific versions) and configurations
* Different environments running the same code can produce different results.
    - Some packages might have updates that changes / breaks the code

* Example (python2 vs python3):
    ```python
    a = 1
    b = 5
    print(a/b)
    ```


---

### How and why to specify environments

- An __environment specification__ is a _description_ of what packages should go in an environment.

* When you _specify_ your environment, it's easier for you (or someone else!) to __reproduce__ your environment.

* Tools turn specifications into environments (and _vice versa_!)
  - `pip` - `requirements.txt`  (python)
  - `conda` - `environment.yml`
  - `Docker` - `Dockerfile`

---

## Python virtual environments

- Python comes with a built-in tool for creating virtual environments

  ```
  python3 -m venv ./my-env
  ```

  This will create a folder called `my-env` containing your virtual environment

* Activate virtual environment

  ```bash
  source ./my-env/bin/activate  # or '. ./my-env/bin/activate'
  ```
  Windows users should use
  ```
  .\my-env\Scripts\activate
  ```

---

- Now you can install the dependencies you need

  ```
  python3 -m pip install pandas
  ```

* To deactivate your virtual environment, type

  ```
  deactivate
  ```

---

### Exercise


Create two different virtual environments called `latest` and `old-pandas`, one where you install the latest version of pandas and one where you install pandas version lower than 2.0

Verify the version using

```bash
python3 -c "import pandas; print(pandas.__version__)"
```

---

### Example

```bash
python3 -m venv latest
. latest/bin/activate
python3 -m pip install pandas
deactivate
```

```bash
python3 -m venv old-pandas
. old-pandas/bin/activate
python3 -m pip install "pandas<2.0"
deactivate
```

---

## Specifying all your dependencies in a file

Specify your python dependencies in a `requirements.in` or `pyproject.toml`
See https://scientificcomputing.github.io/seminar-23-11-2023/environments-slides.html#14 for info about `pyproject.toml`
```
numpy
scipy==1.3.1
sympy>=1.1
git+https://github.com/someuser/someproject.git
git+https://github.com/anotheruser/anotherproject.git@sometag
```
I usually don't specify any versions here unless I know I need an exact version

---

### Pinning exact versions of the libraries you use

- To ensure reproducible results, it is important that you specify the __exact versions__ of the libraries you used __and all their dependencies__
* You can export your current environment at any time in `requirements.txt` format with
  ```
  pip freeze
  ```
* But you shouldn't specify these as your _direct_ dependencies!
* We can use a tool called `pip-compile` (install with `pip install pip-tools`) to pin all the versions based on your `pyproject.toml` or `requirements.in`

---

### Pinning with `pip-tools`

`pip-compile` is like `pip install` followed by `pip freeze`, but without actually installing anything

* Use
  ```
  pip-compile requirements.in
  ```
  or
  ```
  pip-compile pyproject.toml
  ```
  to create a file `requirements.txt` containing all packages you use, directly or indirectly
* You can now install the exact dependencies using the command
  ```
  python3 -m pip install -r requirements.txt
  ```

---


## Conda

[Conda](https://conda.org) is a _generic_ package manager. You can think of it like `pip`, but where _anything_ can be a package (e.g. Python itself, scientific packages like mpich, petsc, fenics-dolfinx).

Key points:

* creates environments, like `venv`
* Python itself is a package
* _All_ packages are binary, there's no "install from source, if needed"
* [conda-forge](https://conda-forge.org) is a community-maintained collection of over 20,000 conda packages
* [miniforge](https://github.com/conda-forge/miniforge) is the best way to get started with conda

---

### Basic conda commands

* `conda install fenics-dolfinx mpich` (`pip install`)
* `conda create --name myproject python=3.10 fenics-dolfinx mpich` (`python3 -m venv`)
* `conda list` (`pip list`)
* `conda env export --name myproject [-f exported.yml]` (`pip freeze`)

---

### Sample environment.yml

```yaml
channels:
  - conda-forge
dependencies:
  - python=3.10
  - fenics-dolfinx
  - mpich
```

Create an environment from an environment file:

```bash
conda env create -n my-paper -f environment.yml
```

---

### conda-lock

[conda-lock](https://conda.github.io/conda-lock/) is a tool for creating "lock files" for conda environments, like `pip-compile`, but for conda:

```
conda install conda-lock
```

```
conda-lock lock --platform linux-64 --platform osx-arm64 -f environment.yml
```

---

## Spack

If you run code on HPC cluster it might be important that all packages are built from source (not pre-built binaries) in order to get it to work.

In these situations, [Spack](https://spack.readthedocs.io/en/latest/index.html) is a great tool, and it also uses the notion of [environments](https://spack.readthedocs.io/en/latest/environments.html)


---

## Docker

[Docker](https://www.docker.com/get-started/) is a tool for packaging an application and all its dependencies, including the _operating system_, together in the form of __images__ and __containers__.

* The user needs to pull an __image__ from a remote registry (or build the image from source)
* create a __container__ (a running instance of an image)
* The user runs the code inside the container

---

### Basic docker commands

* Pull image
    ```
    docker pull <image name>
    ```
    e.g.
    ```
    docker pull ghcr.io/scientificcomputing/fenics:2023-08-14
    ```

* Start new container (set working directory to `home/shared` and share this directory with your current working directory)
    ```
    docker run --name=my-research-code -w /home/shared -v $PWD:/home/shared -it ghcr.io/scientificcomputing/fenics:2023-08-14
    ```

---

- Exit container with `Ctrl+D` or `exit`
* Start existing container
    ```
    docker start my-research-code
    ```
* Execute a running container (jump into it in `bash`)
    ```
    docker exec -it my-research-code bash

* Stop running container
    ```
    docker stop my-research-code
    ```
---

* Remove existing container
    ```
    docker rm my-research-code
    ```
* List downloaded images
    ```
    docker images
    ```
* List containers (omit `-a` to only list running containers)
    ```
    docker ps -a
    ```

---

### Docker development workflow

* The developer needs to write a `Dockerfile` with instructions on how to build and install the dependencies
* The developer needs to build an image and push this to a registry
* Build image from `Dockerfile` with
  ```
  docker build -t my-image .
  ```

---


![bg right:60% fit](https://cdn.shortpixel.ai/spai/q_glossy+w_1456+h_503+to_auto+ret_img/linuxiac.com/wp-content/uploads/2021/06/what-is-docker-container.png)
<p class="small-text">Taken from https://linuxiac.com/what-is-docker-container/</p>

---

### Dockerfile

- Dockerfiles are a series of __directives__,
  each of which modify the filesystem, creating a __layer__.
- The result of a series of layers is an __image__

---

```dockerfile
# Choose latest ubuntu as base image
from ubuntu:latest

# Install python3 with pip as well as git (this might be need to install
# some of the requirements) and clean up afterward to reduce image size
RUN apt-get update && apt-get install -y python3-pip git && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Set the working directory to /repo
# This means that this will be the directory where the commands will be executed
# and the default directory that will be used when the container starts
WORKDIR /repo

# Copy the requirements file into the container at /repo
COPY requirements.txt /repo

# Install any needed packages specified in requirements.txt
# First we also upgrade pip to the latest version
RUN python3 -m pip install --upgrade pip
RUN python3 -m pip install --no-cache-dir -r requirements.txt

# Now we copy the rest of the files into the container at /repo
COPY . /repo
```

---

### We maintain some docker images for scientific computing

* https://github.com/orgs/scientificcomputing/packages

* Also check out https://scientificcomputing.github.io/seminar-23-11-2023/environments-slides.html#35 for a recipe of how to run jupyter inside docker.

* Note: to pull images from GitHub container registry you might need to setup an additional authentication: https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry#authenticating-to-the-container-registry

---

## What to choose?

* Use python virtual environments if you
  - have only python dependencies

* Use conda if
  - you rely on packages with strong dependency on C++/Rust/C/Fortran (e.g Tensorflow, FEniCS)
  - all packages exist on conda (conda-forge / bioconda)

* Use docker if you
  - need full control over the environment
  - require additional packages that are hard to install
  - need the development version of a non-Python dependency (e.g. FEniCS)


---

## When you submit / publish a paper always create a docker image that can reproduce the environment

You can automate this process with GitHub actions. We will have an exercise later :)
![bg right fit](figures/docker_meme.jpg)
Image taken from [reddit](https://www.reddit.com/r/ProgrammerHumor/comments/cw58z7/it_works_on_my_machine/)


---

## Next up: Recording computations

https://scientificcomputing.github.io/phd-retreat-190324/recording-computations-slides
