# Section 13. IO streams primer

## 13.1 Binary and text IO streams

An IO stream can be viewed as a sequence of data (ultimately, these are always just bytes) ‘flowing’ from a ‘source’ to a ‘sink’ (destination). All IO streams fall into two categories:

#### Output Streams
- Data from our programs (values of variables, objects, arrays, etc.) are the “source” while the destination is a file, a socket, the console, or even a region in memory.

#### Input Streams
- Our programs (variables, arrays, etc.) is now the destination, while the source might be a file, a socket, the keyboard, etc.

Java represents characters by their two-byte Unicode codes (the so-called code points). Therefore, there is a problem with reading and writing texts: any text is written using some encoding, so Java must translate a byte or a sequence of bytes corresponding to a character into a two-byte code point and vice versa. Therefore, we have to distinguish byte (binary) streams, where bytes are treated just as bytes, without any modifications, and text (also called character) streams where bytes are subject to some transformations, dependent on the encoding in use. Hence, we end up with four types of IO streams:

#### Output Byte (Binary) Streams
- They all correspond to subclasses of the class `OutputStream` (e.g., `ByteArrayOutputStream`, `FileOutputStream`, `ObjectOutputStream`).

#### Output Text Streams
- They correspond to subclasses of `Writer` (e.g., `BufferedWriter`, `CharArrayWriter`, `OutputStreamWriter`, `PrintWriter`, `StringWriter`).

#### Input Byte Streams
- They correspond to subclasses of `InputStream` (e.g., `ByteArrayInputStream`, `FileInputStream`, `ObjectInputStream`, `StringBufferInputStream`).

#### Input Character Streams
- They correspond to subclasses of `Reader` (e.g., `BufferedReader`, `CharArrayReader`, `InputStreamReader`, `StringReader`).

The main four classes mentioned above are abstract, which means that one cannot create objects of these types, only objects of their concrete subclasses.

Any Java process, like other processes, is given by the operating system three standard streams. They are represented by objects which are static fields of the class `System`:

- `System.out`: representing the standard output stream (by default it is connected to the terminal’s screen).
- `System.in`: representing the standard input stream (by default it is connected to the terminal’s keyboard).
- `System.err`: representing the standard error stream (by default it is connected to the terminal’s screen).

Both `System.out` and `System.err` are of type `PrintStream` which inherits (indirectly) from `OutputStream` (but is designed to handle text output) while `System.in` is of a type inheriting from `InputStream` (and is a binary stream). The difference between `System.out` and `System.err` is that the former is buffered, while the latter is not. 

When we write to a buffered stream, like `System.out`, we are in fact writing to a buffer which will be eventually flushed to its destination (by default, to the screen) in larger chunks. Sometimes such behavior is not desired, especially when we expect some exception handling or when we write to a socket. Printing to unbuffered streams (like `System.err`) is immediate — data is not stored in any buffer, it goes directly to the destination of the stream.

All I/O operations (except those on PrintStream objects, which ‘consumes’ possible exceptions in its methods) may throw exceptions of types extending `IOException` and are checked; therefore, when using them, we have to handle possible failures somehow. Let us consider the following example. In the simple program below, we first write, in a loop, individual bytes of a `long`, starting with the most significant one, to the file. Then we read these bytes back and reconstruct a `long` with the same value. Note the special form of the `try` clause, the so-called try-with-resources. In the round parentheses, we create I/O streams and references to these streams. Note that they will be in scope of the `try` clause. Normally, we have to remember to close all streams that have been opened. The try-with-resources construct, however, takes care of closing streams no matter what happened, even if an exception has been thrown. Therefore, no `finally` clause is needed here.

## Listing 75 KEP-WriteBin/WriteBin.java

```java
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;

public class WriteBin {
    public static void main(String[] args) {
        long before = -284803830071168L;
        System.out.println("before = " + before);
        
        try (OutputStream os = new FileOutputStream("WriteBin.bin")) {
            for (int i = 7; i >= 0; --i) {
                os.write((int) (before >> i * 8));
            }
        } catch(IOException e) {
            e.printStackTrace();
            System.exit(1);
        }

        long after = 0;
        try (InputStream is = new FileInputStream("WriteBin.bin")) {
            for (int i = 0; i < 8; ++i) {
                after = (after << 8) | is.read();
            }
        } catch(IOException e) {
            e.printStackTrace();
            System.exit(1);
        }

        System.out.println("after = " + after);
    }
}

```

The program prints:

```
before = -284803830071168
after  = -284803830071168
```

confirming that we have written and read bytes correctly. Note, that the created file has exactly 8 bytes and contains the full information about our `long`. Writing a `long` in the text form requires up to 20 characters (2.5 times more!). Moreover, conversion of a number to its string representation (and then back) is also a quite expensive operation.

In practice, we don’t have to manipulate the bytes of variables to store them on disk in their binary form — as we will see later, there are special tools in the library that make such tasks trivial.
