# message

A global flag behaving like a lock that enforces the restriction that only one [SetText](../SetText.md) call in [Dialogue mode](../Dialogue%20mode.md) can happen at a given time. It can be used to prevent behaviors to take place until its value is set to false. In [Dialogue mode](../Dialogue%20mode.md) mode, this lock is set to true on setup and released after.

White it is possible to bypass it this restriction by ignore the value of message, doing so is undefined behaviors.
