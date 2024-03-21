# A simple HTTP client in Java

## The hard way using ServerSocket

### 1. Listen for requests

```java
int port = 8080;
ServerSocket listener = new ServerSocket(port);
Socket socket = listener.accept();
```

### 2. Read from a client request

```java
InputStreamReader reader = new InputStreamReader(socket.getInputStream());

CharBuffer charBuffer = CharBuffer.allocate(1_000_000);

reader.read(charBuffer);

charBuffer.flip();
```

### 3. Respond to a request

```java
String body = "Hello, world!";
byte[] bytes = body.getBytes();
String message = "" +
 "HTTP/1.1 200 OK\n" +
 "Content-Type: text/html; charset=UTF-8\n" +
 "Content-Length: " + bytes.length + "\n" +
 "\n" +
 body;

OutputStreamWriter writer = new OutputStreamWriter(socket.getOutputStream());

writer.write(message);
writer.flush();
```

## The easy way using Spring Web MVC

```java
@RestController
public class WelcomeController {
  @GetMapping("/")
  public String home() {
    return "Hello, world!";
  }
}
```

### Spring web annotations

* @Controller - Indicates that an annotated class is a "Controller" (web controller).
* @ResponseBody - Annotation that indicates a method return value should be bound to the web response body(rather than the name of view)
* @RestController - @Controller + @ResponseBody -> @RequestMapping methods assume @ResponseBody
* @RequestMapping - Annotation for mapping web requests onto methods in request-handling classes
* @GetMapping -  @RequestMapping(method = RequestMethod.GET). Annotation for mapping HTTP GET requests onto specific handler methods.
