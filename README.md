# Massa docs

Massa documentation repository.

[![Docs](https://img.shields.io/static/v1?label=massa&message=docs&color=blue)](https://docs.massa.net/)

## Build doc locally

### Setup
wget -N -P ./external/node/ -i ./external/node/config-files.txt
wget -N -P ./external/client/ -i ./external/client/config-files.txt
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
