# Interview : Java Question





Core Java Questions

* Question: Why is a String immutable in Java?
  * Answer: Immutability provides security by preventing unauthorized modification of the data. It also aids in memory management via the String Pool; if multiple String variables are assigned the same literal (e.g., "java"), they simply point to the exact same memory location rather than creating duplicate objects.
* Question: If you want a mutable version of a String, what would you use?
  * Answer: You can use StringBuilder (which is not thread-safe) or StringBuffer (which is thread-safe).
* Question: How does HashMap work internally (after Java 8)?
  * Answer: When data is stored, it calculates a hash code for the key to determine which bucket it belongs to. If a hash collision occurs (multiple keys pointing to the same bucket), the elements are initially stored as a linked list. To optimize performance in Java 8 and later, once the linked list reaches a certain size threshold (typically 8), it is converted into a Red-Black tree.
* Question: How does ConcurrentHashMap handle concurrency differently than HashMap?
  * Answer: `ConcurrentHashMap` maintains concurrency and thread safety by setting locks at the specific key-value pair level rather than locking the entire map. This means each key-value pair can only be manipulated by one thread at a time, but other threads can simultaneously manipulate different key-value pairs without waiting.
* Question: How are static variables stored in memory compared to instance variables?
  * Answer: Static variables are stored in the method area (class loader memory), whereas instance variables are stored inside their respective objects in the heap memory.
* Question: What does the Builder design pattern do?
  * Answer: It solves the problem of instantiating classes with many optional fields where creating numerous specific constructors would be tedious. It allows you to construct an object step-by-step by providing only the fields you specifically need.
* Question: What is the Singleton design pattern and the difference between its lazy and eager implementations?
  * Answer: Singleton ensures a class has only one single instantiated object used throughout the entire application. Eager initialization means the object is created immediately when the application starts, while lazy initialization creates the object only upon the first request for it.
* Question: What is the best way to write the Singleton design pattern?
  * Answer: Using an Enum is the best method because enums are thread-safe by default, and it helps avoid writing complex synchronized blocks for object creation.
* Question: How do you design an immutable class in Java?
  * Answer: Declare the class as `final` so it cannot be overridden. Make all fields private and final. Provide an arguments-based constructor (and exclude any default constructor) so fields are set exactly once during creation. Finally, provide only getters and absolutely no setters to prevent modification after object creation.
