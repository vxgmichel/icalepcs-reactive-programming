name: empty layout
layout: true

---
name: main title
class: center, middle, inverse

Reactive programming
====================

## and how it fits within control systems

[Vincent Michel](https://github.com/vxgmichel) @ ESRF

ICALEPCS 2017 - Barcelona

*

GitHub: [vxgmichel/icalepcs-reactive-programming](https://github.com/vxgmichel/icalepcs-reactive-programming)

Slides: [tinyurl.com/icalepcs-rp](http://tinyurl.com/icalepcs-rp)

*


.footnote.right[.red[**⚠ Warning :**] there will be code chunks!]

---
class: center, middle, inverse

What is reactive programming ?
==============================

***

It's → About → Propagating → Changes
------------------------------------

### *Hum, this looks like a pipeline...*

---
class: center, inverse

**Imperative** → assignment
-----------------------

### **`C = A + B`**

### `C` is **NOT** updated if `A` or `B` changes

***

**Reactive** → definition
---------------------

### **`C := A + B`**

### `C` **IS** updated if `A` or `B` changes

---
class: middle, inverse

Examples
========

## [Python properties](https://docs.python.org/3/library/functions.html#property)

```python3
@property
def C(self):
*   return self.A + self.B
```

``` python3
obj.A = 1
obj.B = 2
assert obj.C == 3
# Here comes the change!
obj.B = 10
assert obj.C == 11
```

### Descriptive, but not actually reactive

---
class: middle, inverse

## [Kivy](kivy.org)/[QML](http://doc.qt.io/qt-5/qmlapplications.html) (declarative approach)

```kivy
TextInput:
    id: A
    text: '0'
TextInput:
    id: B
    text: '0'
Label:
    id: C
*   text: str(int(A.text) + int(B.text))
```

## The kivy app:

.center[![kivy.png](images/kivy.png)]

---
class: middle, inverse

## [Rx](http://reactivex.io/)/[RxPy](https://github.com/ReactiveX/RxPY) (constructive approach)

```python
# A counts every second starting from 0
A = Observable.interval(1000)

# B delays A by 0.5 seconds
B = A.delay(500)

# C sums the latest values from A and B
*C = A.combine_latest(B, lambda a, b: a + b)
```

## Marble diagram

```bash
A stream: ─0───────1───────2───────3──────>
B stream: ─────0───────1───────2───────3──>
C stream: ─────0───1───2───3───4───5───6──>
```
---
class: middle, inverse


How/when is it useful?
======================


### .large.red[⇶　]Event-based channels ≈ reactive data streams

### .large.red[λ　]Less state to manage → more functionnal, less side effect

### .large.red[≝　]A declarative interface hides the implementation logic

---
class: middle, inverse

What about control systems?
===========================

.center[![control-system](images/control-system.png)]

---

layout: true

2. MAX-IV event policy
======================

---
class: center, middle

---
class: top, left

Why events?
-----------

- **Golden rule**:

  * Monitoring shoudn't affect the world

--

- **Consequence**:

  * Read requests from clients shouldn't trigger a hardware request

  * The communication with the hardware is handled by the server

  * Regardless of the number of clients (could be 0, 1 or 10)

  * Reading from the cache is OK, but PUB/SUB is better!


---

But what about RPC (REQ/REP)?
-----------------------------

- RPC is perfectly fine for all other requests:

  * Commands

  * Writing attributes

- Because it's the result of a **user decision**

--

Then what kind of events?
-------------------------

- Change event **without tango filters**

  * Use `self.set_change_event(True, False)`

- Periodic event as a fallback solution


---

Event configuration
-------------------


- Low-level (hardware oriented) TANGO devices

  * **Type A**: use internal polling and push change event from the code

  * **Type B**: use command polling and push change event from the code

  * **Type C**: use attribute polling and rely on periodic events

--

- High-level TANGO devices

  * **Facade devices**:

     + subscribe to change and periodic events

     + propagate changes (react!)

     + push change event from the code


---
layout: true

3. The facade device library
============================

---
class: center, middle

[MaxIV-KitsControls/tango-facadedevice](https://github.com/MaxIV-KitsControls/tango-facadedevice)

```
-
            +------------+             
            |            |             
Events +--->+   React!   +----> Events 
            |            |             
            +------------+             
-
```

---
class: top, left

Example (C := A + B)
-------------------

``` python
class Addtion(Facade):

    A = proxy_attribute(
        dtype=float,
        property_name='AAttribute')

    B = proxy_attribute(
        dtype=float,
        property_name='BAttribute')

    @logical_attribute(
        dtype=float,
        bind=['A', 'B'])
    def C(self, a, b):
        return a + b
```

---

Features
--------

- The `Facade` base class:

  * is based on `tango.server.Device`

  * supports inheritance

--

- Attributes and commands:

  * `local_attribute` - for local settings

  * `logical_attribute` - for logical relationships

  * `state_attribute` - to make state and status reactive

  * `proxy_attribute` - for forwarding remote attributes

  * `combined_attribute` - for aggregating remote attributes

  * `proxy_command` - for exposing a remote command

---

The project
-----------

- Documentation on readthedocs:

   * [tango-facadedevice.readthedocs.io](http://tango-facadedevice.readthedocs.io)

   * Full tutorial, API reference and examples

- Unit-tested:

   * Continuous integration with TravisCI

   * 100% of code coverage :)

- Not released on PyPI yet

   * v1.0.0dev

---

name: final
layout: false
class: center, middle

Thank you!
==========

### Questions ?

___

Presentation written in `Markdown` and rendered by [remark](http://remarkjs.com/)

Sources for this presentation can be found on github

[https://github.com/vxgmichel/facade-presentation](https://github.com/vxgmichel/facade-presentation)
