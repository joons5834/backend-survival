# Application-level security

## Authentication

* is answering a question of "Who is the user?"
* The user tells who he/she is via an ID card in the offline world
* The user tells who he/she is via username and password , OTP etc.
* From the user experience perspective, authentication(login) does not repeat once it is done
* Technically speaking, given the statelessness of HTTP, each request requires authentication

## Authorization

* is answering a question of "Who is allowed?"
* Suppose an admin page where only admins have access to all the orders
  * The current user has to be proved admin.(authentication)
  * Non-admin users are not allowed access to the admin page(authorization)

## How users, clients and servers view authorization and authentication

* Once users authenticate through login page, users can only know there has been authorization when they are denied access
* Clients get tokens as a result of login process which are managed via cookies or localStorage and sent to the server at each request
* Servers authenticate each request. They verify tokens to identify users(authentication) and check whether users have appropriate access to the resource to deny or permit the access.
  * In Spring Web MVC, HandlerInterceptor runs the code before a request is sent to controllers.
  * In Spring Security, SecurityFilterChain runs the code before a request is sent to the controllers.

## Encryption

* the process of encoding information
  * Ideally, only authorized parties can decipher a ciphertext back to plaintext and access the original information.
* One-way encryption(irreversible) : cannot decipher encrypted text
  * used for encrypting password before saving it onto a server
  * safer than decipherable encryption in case of data breach
  * cryptographic hashing algorithm is used for one-way encryption
  * bcrypt and scrypt was commonly used but Argon2 is recommended nowadays

## Authentication in Spring Security

1. Add SecurityConfig
1. Add SecurityFilterChain within SecurityConfig
1. add authentication filter using `addFiterBefore`

```java
@Configuration
@EnableWebSecurity
@EnableMethodSecurity(securedEnabled = true)
public class WebSecurityConfig {

    // ...
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http)
            throws Exception {

        http.csrf().disable();
        http.addFilterBefore(
                authenticationFilter, BasicAuthenticationFilter.class);
        http.authorizeHttpRequests()
                .requestMatchers(HttpMethod.POST, "/session").permitAll()
                .requestMatchers(HttpMethod.POST, "/users").permitAll()
                .anyRequest().authenticated();

        return http.build();
    }
    //...
```

1. extend `OncePerRequestFilter` and override `doFilterInternal`
1. parse and verify the token
1. set returned Authentication into a SecurityContext

```java
@Component
public class AccessTokenAuthenticationFilter extends OncePerRequestFilter {

    //...
    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain filterChain) throws ServletException, IOException {

        String accessToken = parseAccessToken(request);

        Authentication authentication =
                accessTokenService.authenticate(accessToken);

        SecurityContextHolder.getContext()
                .setAuthentication(authentication);


        filterChain.doFilter(request, response);

    }
    //...
```

accessTokenService.authenticate

```java
    public Authentication authenticate(String accessToken) {
        if (!accessTokenGenerator.verify(accessToken)) {
            return null;
        }
        return userDetailsDao.findByAccessToken(accessToken)
                .map(userDetails ->
                        UsernamePasswordAuthenticationToken.authenticated(
                                userDetails.getUsername(),
                                userDetails.getPassword(),
                                userDetails.getAuthorities()))
                .orElse(null);
    }
```

## Authorization in Spring Security

1. Enable `@EnableMethodSecurity(securedEnabled = true)`
1. Annotate the controller methods with `@Secured` annotation

```java
    //...
@Configuration
@EnableWebSecurity
@EnableMethodSecurity(securedEnabled = true)
public class WebSecurityConfig {
    //...
```

```java
@RestController
public class WelcomeController {
    @GetMapping("/")
    public String home() {
        return "Hello, world!";
    }

    @GetMapping("/admin")
    @Secured("ROLE_ADMIN")
    public String admin() {
        return "Hello, world!";
    }

    @GetMapping("/user")
    @Secured({"ROLE_USER", "ROLE_ADMIN"})
    public String user() {
        return "Hello, world!";
    }
}
```

## Password Encryption in Spring Security

1. Add a PasswordEncoder bean 
1. use `encode()` of a passwordEncoder

```java
@Configuration
@EnableWebSecurity
@EnableMethodSecurity(securedEnabled = true)
public class WebSecurityConfig {
    // ...
    @Bean
    public PasswordEncoder passwordEncoder() {
        return Argon2PasswordEncoder.defaultsForSpringSecurity_v5_8();
    }
}

```

```java
//...
String encodedPassword = passwordEncoder.encode(password);
//...
```

## JWT

* JSON Web Token (JWT) is an open standard (RFC 7519) that defines a compact and self-contained way for securely transmitting information between parties as a JSON object.
* This information can be verified and trusted because it is digitally signed.
* Follows the structure of `{Header}.{Payload}.{Signature}`

### JWT in Spring Security

1. Add `implementation 'com.auth0:java-jwt:4.4.0'` to `build.gradle`
1. create and verify as the following

```java
@Component
public class AccessTokenGenerator {
    private final Algorithm algorithm;

    public AccessTokenGenerator(
            @Value("${jwt.secret}")
            String secret) {
        this.algorithm = Algorithm.HMAC256(secret);
    }

    public String generate(String userId) {
        return JWT.create()
                .withClaim("userId", userId)
                .withExpiresAt(Instant.now().plus(24, ChronoUnit.HOURS))
                .sign(algorithm);
    }

    public boolean verify(String accessToken) {
        try {
            JWTVerifier verifier = JWT.require(algorithm).build();
            DecodedJWT verify = verifier.verify(accessToken);

            // You can obtain userId here and use it elsewhere
            String userId = verify.getClaim("userId").asString();

            return true;
        } catch (JWTVerificationException e) {
            return false;
        }
    }
}
```
