# pymitter

[![Package Status](https://img.shields.io/pypi/v/pymitter.svg?style=flat)](https://pypi.python.org/pypi/pymitter) [![License](https://img.shields.io/github/license/riga/pymitter.svg)](https://github.com/riga/pymitter/blob/master/LICENSE)
[![Lint and test](https://github.com/riga/pymitter/actions/workflows/lint_and_test.yml/badge.svg)](https://github.com/riga/pymitter/actions/workflows/lint_and_test.yml)

Python port of the extended Node.js EventEmitter 2 approach of https://github.com/asyncly/EventEmitter2 providing namespaces, wildcards and TTL.


## Features

- Namespaces with wildcards
- Times to listen (TTL)
- Usage via decorators or callbacks
- Lightweight implementation, good performance


## Installation

*pymitter* is a registered [PyPI module](https://pypi.python.org/pypi/pymitter), so the installation with *pip* is quite easy:

```shell
pip install pymitter
```

The last version with Python 2 support was v0.3.2 ([tag](https://github.com/riga/pymitter/tree/v0.3.2), [PypI](https://pypi.org/project/pymitter/0.3.2)).


## Examples

### Basic usage

```python
from pymitter import EventEmitter


ee = EventEmitter()


# decorator usage
@ee.on("myevent")
def handler1(arg):
    print("handler1 called with", arg)


# callback usage
def handler2(arg):
    print("handler2 called with", arg)


ee.on("myotherevent", handler2)


# emit
ee.emit("myevent", "foo")
# -> "handler1 called with foo"

ee.emit("myotherevent", "bar")
# -> "handler2 called with bar"
```


### TTL (times to listen)

```python
from pymitter import EventEmitter


ee = EventEmitter()


@ee.once("myevent")
def handler1():
    print("handler1 called")


@ee.on("myevent", ttl=10)
def handler2():
    print("handler2 called")


ee.emit("myevent")
# -> "handler1 called"
# -> "handler2 called"

ee.emit("myevent")
# -> "handler2 called"
```


### Wildcards

```python
from pymitter import EventEmitter


ee = EventEmitter(wildcard=True)


@ee.on("myevent.foo")
def handler1():
    print("handler1 called")


@ee.on("myevent.bar")
def handler2():
    print("handler2 called")


@ee.on("myevent.*")
def hander3():
    print("handler3 called")


ee.emit("myevent.foo")
# -> "handler1 called"
# -> "handler3 called"

ee.emit("myevent.bar")
# -> "handler2 called"
# -> "handler3 called"

ee.emit("myevent.*")
# -> "handler1 called"
# -> "handler2 called"
# -> "handler3 called"
```

## API


### ``EventEmitter(wildcard=False, delimiter=".", new_listener=False, max_listeners=-1)``

EventEmitter constructor. **Note**: always use *kwargs* for configuration. When *wildcard* is
*True*, wildcards are used as shown in [this example](#wildcards). *delimiter* is used to seperate
namespaces within events. If *new_listener* is *True*, the *"new_listener"* event is emitted every
time a new listener is registered. Functions listening to this event are passed
``(func, event=None)``. *max_listeners* defines the maximum number of listeners per event. Negative
values mean infinity.

- #### ``on(event, func=None, ttl=-1)``
    Registers a function to an event. When *func* is *None*, decorator usage is assumed. *ttl*
    defines the times to listen. Negative values mean infinity. Returns the function.

- #### ``once(event, func=None)``
    Registers a function to an event with ``ttl = 1``. When *func* is *None*, decorator usage is
    assumed. Returns the function.

- #### ``on_any(func=None)``
    Registers a function that is called every time an event is emitted. When *func* is *None*,
    decorator usage is assumed. Returns the function.

- #### ``off(event, func=None)``
    Removes a function that is registered to an event. When *func* is *None*, decorator usage is
    assumed. Returns the function.

- #### ``off_any(func=None)``
    Removes a function that was registered via ``on_any()``. When *func* is *None*, decorator usage
    is assumed. Returns the function.

- #### ``off_all()``
    Removes all functions of all events.

- #### ``listeners(event)``
    Returns all functions that are registered to an event. Wildcards are not applied.

- #### ``listeners_any()``
    Returns all functions that were registered using ``on_any()``.

- #### ``listeners_all()``
    Returns all registered functions.

- #### ``emit(event, *args, **kwargs)``
    Emits an event. All functions of events that match *event* are invoked with *args* and *kwargs*
    in the exact order of their registeration. Wildcards might be applied.


## Development

- Source hosted at [GitHub](https://github.com/riga/pymitter)
- Python module hostet at [PyPI](https://pypi.python.org/pypi/pymitter)
- Report issues, questions, feature requests on [GitHub Issues](https://github.com/riga/pymitter/issues)
