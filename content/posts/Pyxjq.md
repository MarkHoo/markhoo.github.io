---
title: Python中的小技巧
date: 2018-07-15 12:50:04
cover: 
categories: Python
tags:
- Python
---

> Python中的小技巧

<!--more-->

```python
#coding:utf-8


# NO.1
def dispatch_if(operator, x, y):
    if operator == 'add':
        return x + y
    elif operator == 'sub':
        return x - y
    elif operator == 'mul':
        return x * y
    elif operator == 'div':
        return x / y
    else:
        return None


def dispatch_dict(operator, x, y):
    return {
        'add': lambda: x + y,
        'sub': lambda: x - y,
        'mul': lambda: x * y,
        'div': lambda: x / y,
    }.get(operator, lambda: None)()


print(dispatch_if('sub', 5, 9))
print(dispatch_dict('mul', 3, 7))
print(dispatch_if('unknown', 2, 8))
print(dispatch_dict('unknown', 2, 8))


# NO.2
def myfunc(a, b):
	return a + b

funcs = [myfunc]
print(funcs[0])
print(funcs[0](2, 3))


# NO.3
my_mapping = {'a': 23, 'b': 42, 'c': 0xc0ffee}
print(my_mapping)

import json
print(json.dumps(my_mapping, indent=4, sort_keys=True))

# json.dumps({all: 'yup'})


# NO.4
name_for_userid = {
    382: "Alice",
    590: "Bob",
    951: "Dilbert",
}

def greeting(userid):
    return "Hi %s!" % name_for_userid.get(userid, "there")

print(greeting(951))

# NO.5
xs = {'a': 4, 'b': 3, 'c': 2, 'd': 1}

S = (sorted(xs.items(), key=lambda x: x[1]))
print(S)
# Or:

import operator
S = sorted(xs.items(), key=operator.itemgetter(1))
print(S)
```