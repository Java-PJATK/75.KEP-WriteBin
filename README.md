# KEP-WriteBin

In the simple program below, we first write, in a loop, individual bytes of a long, starting with the most significant one, to the file.  
  
Then we read these bytes back and reconstruct a long with the same value. Note the special form of the try clause, the so called try-with-resources. In the round parentheses,
we create I/O streams and references to these streams. Note that they will be in scope of the try clause. Normally, we have to remember to close all streams that have been opened.  
  
The try-with-resources construct, however, takes care of closing streams no matter what happened, even if an exception has been thrown. Therefore, no finally clause is needed here.

The program prints  
before = -284803830071168  
after = -284803830071168  
  
confirming that we have written and read bytes correctly.  
  
Note, that the created file has exactly 8 bytes and contains the full information about our long. Writing a long in the text form requires up to 20 characters (2.5 times more!). Moreover, conversion of a number to its string representation (and then back) is also a quite expensive operation.  
  
In practice, we don’t have to manipulate the bytes of variables to store them on disk in their binary form — as we will see later, there are special tools in the library that make such tasks trivial.
