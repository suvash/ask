# Union operator for dictionaries

Introduced in Python 3.9 (via PEP 584), `|` symbol can be used to merge dictionaries.
```python
x = {"this": "that", "and": "or"}
y = {"whom": "when", "human": "bird", "this": "there"}
x | y
# {'this': 'there', 'and': 'or', 'whom': 'when', 'human': 'bird'}

# Is equivalent to
{**x, **y}
# {'this': 'there', 'and': 'or', 'whom': 'when', 'human': 'bird'}
```

Note that the values get overridden by the variable to the right, similar to the splat method.

## Source
- https://peps.python.org/pep-0584/
