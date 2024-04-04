# CORS

## Same-Origin Policy

* It restricts how a document or script loaded by one origin can interact with a resource from another origin

* Under the policy, a web browser permits scripts contained in a first web page to access data in a second web page, but only if both web pages have the same origin.

  * Two URLs have the same origin if the protocol, port (if specified), and host are the same for both.

## CORS(Cross-Origin Resource Sharing): Allowing cross-origin access

* CORS is a part of HTTP that lets servers specify any other hosts from which a browser should permit loading of content.

* Add a "Access-Control-Allow-Origin" header to a response, of which value is an allowed origin.

### CORS in Spring

* Add a "Access-Control-Allow-Origin" header yourself.

```java
@GetMapping
public List<PostDto> list(
 HttpServletResponse response
) {
 response.addHeader("Access-Control-Allow-Origin", "http://localhost:3000");
```

* Use `@CrossOrigin` annotation

```java
@CrossOrigin("http://localhost:3000")
```

* Use `addCorsMappings` of `WebMvcConfigurer`

```java
@Bean
 public WebMvcConfigurer webMvcConfigurer() {
  return new WebMvcConfigurer() {
  
  @Override
  public void addCorsMappings(CorsRegistry registry) {
   registry.addMapping("/**")
       .allowedMethods("GET", "POST", "PATCH", "DELETE", "OPTIONS")
       .allowedOrigins("http://localhost:3000");
  }
 };
}
```
