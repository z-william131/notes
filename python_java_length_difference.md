## Why Python emoji length is different than Java?

### Background
Recently I built a text processing API for a working project. The API is called in Java and runs on Python. Later, I encountered a bug that caused by offset difference
 that processed text in Python and used the result on Java. 
💩

### Reproduce the Problem
Let's use poop emoji as an example. In python the emoji length is 1:
```
print(len("💩"))    # print out 1
```
However, in Java the emoji length is 2:
```
System.out.println("💩".length());    // print out 2
```

### Code Unit VS Code Point in Unicode
There are two terms we need to understand before solving this problem. A *code point* is a numerical value that represents a Unicode character. You can think of this number as an index in to the Unicode character set. A code point is encoded as a sequence of integers(called *code unit*), whose bit size depends upon the selected character encoding methods. An example of character encoding method would be UTF-8. The size of UTF-8 code unit is 8 bits. 

### Why the length of 💩 is 2 in Java
Java uses UTF-16 to encode characters. The `.length()` function in Java returns the number of 16-bit Unicode characters in string. It equals to the number of code unit in string. In UTF-16, "💩" contains only one code point that is encoded by 2 code units each 16-bits in length. Therefore the length of 💩 is 2. 

### Why the length of 💩 is 1 in Python
Python uses UTF-8 to encode characters. The `len()` function in Python returns the number of characters in the string. It equals to the number of code point in string. Therefore the length of 💩 is 1. 

### How to get the Java length of String in Python
Since the length of the string in Java equals to the number of 16-bit Unicode characters, It should also equals to the byte size of the string times 2. Therefore we can calculate the byte size of the string in Python to calculate the Java length.
```
poop_in_java = "💩".encode("utf-16")
poop_byte_size = len(poop_in_java)
poop_len_in_java = int(poop_byte_size / 2)
print(poop_len_in_java)    # print out 2
```

### How to get the Java length of String in Python
```
System.out.println("💩".codePoints().count());    // print out 1
```
