# Massa docs [![Docs](https://img.shields.io/static/v1?label=massa&message=docs&color=blue)](https://docs.massa.net/)

<div align="center">
  <img src="_static/MassaDark.png?raw=true">
</div>

---

This repository contains the contents for the Massa Developer Documentations
deployed at [https://docs.massa.net](https://docs.massa.net).

## Contributing

Contributing to the documentation is a great way to get involved with the Massa community! Here's some things you need
to know to get started.

The site is built using [Sphynx](https://www.sphinx-doc.org/).

### Contents

- All the documentation text are located in the directory corresponding to the section; creating a sub-dir is strongly
  recommended if there are multiple sub-sections for a doc.
The left sidebar of the page is controlled by sidebars.js.
- Extensive documentation for Sphynx can be found [here](https://www.sphinx-doc.org/).

### Pull Request (PR)

- All PRs should be made against the `main` branch.
- Following a successful build, you can verify your changes on massa--*.org.readthedocs.build/en/* where \* is you PR
  id.
- Once your PR is merged into `main`, [https://docs.massa.net/](https://docs.massa.net/)
  will be updated with your changes.

### Build doc locally

#### Setup
wget -N -P ./external/node/ -i ./external/node/config-files.txt
wget -N -P ./external/client/ -i ./external/client/config-files.txt
python -m venv venv
venv/bin/python -m pip install -r requirements.txt

#### Build

[Edit Makefile] change the following line from:

* SPHINXBUILD   ?= sphinx-build

TO

* SPHINXBUILD   ?= venv/bin/sphinx-build

then run:

```commandline
make html
```
