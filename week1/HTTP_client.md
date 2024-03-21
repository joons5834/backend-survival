# A simple HTTP client in Java

## 1. Connect to a server

```java
String host = "example.com";
int port = 80;
Socket socket = new Socket(host, port);
```

## 2. Make an HTTP request

```java
String message = "" +
 "GET / HTTP/1.1\n" +
 "Host: example.com\n" +
 "\n";

OutputStreamWriter writer = new OutputStreamWriter(socket.getOutputStream());

writer.write(message);
writer.flush();
```

### Why do we flush writer?

* If bytes written is buffered, you need to make sure such bytes should be written to intended destination immediately.

### What is a buffer?

* Large chunks of a file are read from disk, and then accessed a byte or character at a time.

* Stream data is written to a cache in large chunks, and then accessed a byte or character at a time.

### Why use buffer?

* To improve IO performance
  * To avoid accessing the disk/OS etc.
  * To avoid method calls
* See [Tuning Java I/O Performance](https://www.oracle.com/technical-resources/articles/javase/perftuning.html) for details.

## 3. Read a response

```java
InputStreamReader reader = new InputStreamReader(socket.getInputStream());

CharBuffer charBuffer = CharBuffer.allocate(1_000_000);

reader.read(charBuffer);

charBuffer.flip();

System.out.println(charBuffer.toString());
```

### Why do we flip the buffer?

* The limit is set to current position
* The position is set to zero
* After a sequence of channel-read or put operations, invoke this method to prepare for a sequence of channel-write or relative get operations.
* This means if you reach the end of the write operation, you need to mark the end of content and move to the beginning of the content to read the whole content you wrote!

## 4. Close the connection

```java
socket.close();
```
