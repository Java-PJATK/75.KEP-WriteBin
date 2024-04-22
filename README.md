# KEP-WriteBin

In the simple program below, we first write, in a loop, individual bytes of a long, starting with the most significant one, to the file.  
  
Then we read these bytes back and reconstruct a long with the same value. Note the special form of the try clause, the so called try-with-resources. In the round parentheses,
we create I/O streams and references to these streams. Note that they will be in scope of the try clause. Normally, we have to remember to close all streams that have been opened.  
  
The try-with-resources construct, however, takes care of closing streams no matter what happened, even if an exception has been thrown. Therefore, no finally clause is needed here.
