# Que - Why are immutable objects are thread safe?

**Ans:** 
 Immutable objects are thread-safe because their state cannot be changed after they are created. Since the object cannot be modified, multiple threads can access the same instance simultaneously without causing inconsistencies or race conditions.
 There is no need for synchronization because no thread can change the object's state. All threads only read the same data, which makes immutable objects inherently thread-safe.

- Simple Example
  Example with String (which is immutable):
  String str = "Hello";

- If multiple threads access str, none of them can modify it. Any modification creates a new object:
    str = str.concat(" World");
    This creates a new String object, leaving the original unchanged.


**Key Points**
- Immutable objects prevent race conditions.
- No synchronization is required.
- They are safe for shared access across threads.
- They are commonly used in concurrent programming.
