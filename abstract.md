Facade devices, a reactive approach to Tango
--------------------------------------------

[Reactive programming][1] is an asynchronous programming paradigm oriented around data streams and the propagation of change. Instead of using the traditional imperative approach of maintaining the integrity of variables, a reactive program expresses the relationship between data streams. It is especially useful for user interfaces and fits very well within an event-based system. A reactive program can either define relationships using a descriptive language (e.g [QML][2]), or build those relationships imperatively (e.g. [ReactiveX][3]).

MAX-IV decided to adopt events as the main communication channel in order to increase both the responsiveness and reliability of its control system. That means the tango devices do not perform read hardware operation on client request but instead maintain a reliable communication with the hardware while publishing the data through event channels. This also allows higher levels of tango device to be built on top of the hardware-oriented devices.

Since those high-level devices are fully based on events (they both subscribe and publish), they make very good candidates for reactive programming. More precisely, it is possible to describe the attributes of those devices as relationships, where the input data comes from the event channels of lower-level devices. The [facadedevice][4] python library is an attempt to implement the reactive machinery within a tango device base class, and provides a descriptive API for defining the relationships in a clear and concise way.

[1]: https://en.wikipedia.org/wiki/Reactive_programming
[2]: https://en.wikipedia.org/wiki/QML
[3]: https://en.wikipedia.org/wiki/Reactive_extensions
[4]: https://github.com/MaxIV-KitsControls/tango-facadedevice


