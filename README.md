# jsondiff

Diff JSON and JSON-like structures in Python.

## How to create / enable virtual env

```
python3 -m venv /path/to/jsondiff
source /path/to/jsondiff/bin/activate
```

## install packages
```
pip install -r requirements-dev.txt
```

## run tests
```
cd into tests/

pytest
```

## Installation

``pip install jsondiff``

## Quickstart

```python
>>> import jsondiff as jd
>>> from jsondiff import diff

>>> diff({'a': 1, 'b': 2}, {'b': 3, 'c': 4})
{'c': 4, 'b': 3, delete: ['a']}

>>> diff(['a', 'b', 'c'], ['a', 'b', 'c', 'd'])
{insert: [(3, 'd')]}

>>> diff(['a', 'b', 'c'], ['a', 'c'])
{delete: [1]}

# Typical diff looks like what you'd expect...
>>> diff({'a': [0, {'b': 4}, 1]}, {'a': [0, {'b': 5}, 1]})
{'a': {1: {'b': 5}}}

# You can exclude some jsonpaths from the diff (doesn't work if the value types are different)
>>> diff({'a': 1, 'b': {'b1': 20, 'b2': 21}, 'c': 3},  {'a': 1, 'b': {'b1': 22, 'b2': 23}, 'c': 30}, exclude_paths=['b.b1', 'c'])
{'b': {'b2': 23}}

# ...but similarity is taken into account
>>> diff({'a': [0, {'b': 4}, 1]}, {'a': [0, {'c': 5}, 1]})
{'a': {insert: [(1, {'c': 5})], delete: [1]}}

# Support for various diff syntaxes
>>> diff({'a': 1, 'b': 2}, {'b': 3, 'c': 4}, syntax='explicit')
{insert: {'c': 4}, update: {'b': 3}, delete: ['a']}

>>> diff({'a': 1, 'b': 2}, {'b': 3, 'c': 4}, syntax='symmetric')
{insert: {'c': 4}, 'b': [2, 3], delete: {'a': 1}}

>>> diff({'list': [1, 2, 3], "poplist": [1, 2, 3]}, {'list': [1, 3]}, syntax="rightonly")
{"list": [1, 3], delete: ["poplist"]}

# Special handling of sets
>>> diff({'a', 'b', 'c'}, {'a', 'c', 'd'})
{discard: set(['b']), add: set(['d'])}

# Load and dump JSON
>>> print diff('["a", "b", "c"]', '["a", "c", "d"]', load=True, dump=True)
{"$delete": [1], "$insert": [[2, "d"]]}

# NOTE: Default keys in the result are objects, not strings!
>>> d = diff({'a': 1, 'delete': 2}, {'b': 3, 'delete': 4})
>>> d
{'delete': 4, 'b': 3, delete: ['a']}
>>> d[jd.delete]
['a']
>>> d['delete']
4
# Alternatively, you can use marshal=True to get back strings with a leading $
>>> diff({'a': 1, 'delete': 2}, {'b': 3, 'delete': 4}, marshal=True)
{'delete': 4, 'b': 3, '$delete': ['a']}
```

## Command Line Client

Usage:
```
jdiff [-h] [-p] [-s {compact,symmetric,explicit}] [-i INDENT] [-f {json,yaml}] first second

positional arguments:
  first
  second

optional arguments:
  -h, --help            show this help message and exit
  -p, --patch
  -s {compact,symmetric,explicit}, --syntax {compact,symmetric,explicit}
                        Diff syntax controls how differences are rendered (default: compact)
  -i INDENT, --indent INDENT
                        Number of spaces to indent. None is compact, no indentation. (default: None)
  -f {json,yaml}, --format {json,yaml}
                        Specify file format for input and dump (default: json)
```

Examples:

```bash
$ jdiff a.json b.json -i 2

$ jdiff a.json b.json -i 2 -s symmetric

$ jdiff a.yaml b.yaml -f yaml -s symmetric
```

## Development

Install development dependencies and test locally with

```bash
pip install -r requirements-dev.txt
# ... do your work ... add tests ...
pytest
```

## Installing From Source

To install from source run

```bash
pip install .
```

This will install the library and cli for `jsondiff` as well as its runtime
dependencies.


## Testing before release

```bash
python -m build
twine check dist/*
```
