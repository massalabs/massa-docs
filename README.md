# Massa docs

Massa documentation repository.

[![Docs](https://img.shields.io/static/v1?label=massa&message=docs&color=blue)](https://docs.massa.net/)

## Documentation process

- The goal is to improve as a team and when we have issues try to identify people in each team that can help. (Core: Damir, Hawkey. Innovation: Ena, ??)
- It might also be a good idea to do cross team reviews. As the documentation is intended to external people, it should be understandable by someone which is not a technical expert at Massa.
- We need to make sure that concepts we introduce in the later sections are explained in a section dedicated to them. Like “operation datastore” for example.

## Build doc locally

### Setup

python -m venv venv
venv/bin/python -m pip install -r requirements.txt

### Build

[Edit Makefile] change the following line from:

* SPHINXBUILD   ?= sphinx-build

TO

* SPHINXBUILD   ?= venv/bin/sphinx-build

then run:

```commandline
make html
```
