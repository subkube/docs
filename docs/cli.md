SubKube offers a simple CLI tool to interact with your SubKube Projects and
Namespaces, called `subctl`.

## Installation

The `subctl` is written in python, using click, and as such is easiest
installed using `pip` as shown below, but self-contained binaries are available
for most platforms as well.

```
pip install git+https://github.com/subkube/subctl
```


## Usage

```
subctl login

subctl get project
subctl get namespace

subctl use project
subctl use namespace

subctl create namespace
subctl create project
```
