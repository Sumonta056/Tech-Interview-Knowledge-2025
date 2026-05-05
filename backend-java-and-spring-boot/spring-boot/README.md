# Spring Boot

After reviewing thousands of pull requests, these patterns keep showing up, especially from developers in their first 2 years.

Here is what trips people up the most:

→ Using == instead of .equals() for String comparison\
→ Mutating Date objects when LocalDate exists\
→ Throwing checked exceptions for programming errors\
→ Using raw types instead of generics\
→ Concatenating Strings in loops instead of StringBuilder\
→ Writing nested null checks instead of using Optional\
→ Defaulting to arrays when ArrayList gives you flexibility\
→ Wrapping everything in synchronized when ConcurrentHashMap exists\
→ Catching Exception instead of the specific type you expect\
→ Making utility methods static when they should be instance methods\
→ Using new String("hello") instead of string literals\
→ Using Integer when int would suffice\
→ Repeating type args instead of using the diamond operator\
→ Manual close() in finally instead of try-with-resources\
→ Using static final int constants instead of enums\
→ Writing verbose for-if-add loops instead of streams\
→ Using Arrays.asList when List.of gives true immutability\
→ Spelling out full types when var keeps code clean\
→ Writing boilerplate classes when records do the job\
→ Concatenating strings with \n instead of using text blocks



