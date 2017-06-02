name: empty layout
layout: true

---
name: main title
class: center, middle

Facade devices
==============

## A reactive approach to Tango

Vincent Michel @ ESRF

TANGO Meeting 2017 - Florence

[vxgmichel.github.io/facade-presentation](https://vxgmichel.github.io/facade-presentation)

---
layout: true

1. Reactive programming
=======================

---
class: center, middle

---
class: top, left

Definition
----------

 - Asynchronous paradigm oriented around data streams

 - Based on the propagations of changes

--

Imperative vs reactive
----------------------

 - Imperative → assignment:  C = A + B

   * C is not updaated if A or B changes

 - Reactive → definition:  C := A + B

   * C is updated if A or B changes

---

Examples
--------

  - [Python properties](https://docs.python.org/3/library/functions.html#property) (not actually reactive)

    ```python3
    @property
    def C(self):
        return self.A + self.B
    ```

--

  - [Kivy](kivy.org)/[QML](http://doc.qt.io/qt-5/qmlapplications.html) (descriptive approach)

    ```kivy
    <ReactiveWidget>:
        TextInput:
            id: A
            text: '0'
        TextInput:
            id: B
            text: '0'
        Label:
            id: C
            text: str(int(A.text) + int(B.text))
    ```

---

  - [Rx](http://reactivex.io/)/[RxPy](https://github.com/ReactiveX/RxPY) (constructive approach)

    ```python
    from rx import Observable
    A = Observable.interval(1000)
    B = Observable.interval(1000).delay(500)
    C = A.combine_latest(B, lambda a, b: a + b)
    C.subscribe(print)
    ```

    A counts from 0 every second

    B counts from 0 every second, starting 0.5 second after A

    C sums A and B

    Output:

      ```raw
      0
      1
      2
      ... and so on every 0.5 second
      ```

---

How's that useful for us?
-------------------------

  - Event-based communication channels ≈ reactive data streams

  - No state to manage => more functionnal, less side effect

  - A descritive interface hides the implementation logic

--

⇒ Facade device library
-----------------------

Implement a reactive machinery within a TANGO device base class

[MaxIV-KitsControls/tango-facadedevice](https://github.com/MaxIV-KitsControls/tango-facadedevice)

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
