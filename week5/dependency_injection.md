# Choosing and Using elements I need

## Factory

```Java
public class GetPostService {
    private final PostRepository postRepository;

    public PostService() {
        postRepository = new PostRepository();
    }
```

```Java
public class UpdatePostService {
    private final PostRepository postRepository;

    public PostService() {
        postRepository = new PostRepository();
    }
```

How do I ensure different postServices refer to the same repository object?

* Define and use an object dedicated for creating and managing repository objects
  * Use a `factory`(an object for creating other objects) for repository objects
* Make sure the factory return the same repository object despite repetitive calls of the factory (singleton pattern)

## Dependency Injection

```Java
public class GetPostService {
    private final PostRepository postRepository;

    public PostService() {
        postRepository = Factory.postRepository();
    }
```

```Java
public class Factory {
    private static PostRepository postRepository;

    public static PostRepository postRepository() {
        if (postRepository == null) {
            postRepository = new PostRepository();
        }
        return postRepository;
    }
}
```

What if `PostRepository` is an interface and there are multiple implementations of Repository interface ...

And you want to choose which implementation to use for various deployment modes?

Or you have complex rules for which implementation to choose, which, if incorprated into as complex `if` statements in you code, hurt readability and maintainability of your code?

Or the code base is so huge that it is hard to follow and maintain dependencies between objects?

* Separate configuration from use(quoted from and inspired by [Martin Fowler's words on DI](https://martinfowler.com/articles/injection.html))
  * Suppose there is a framework for creating/managing/plugging objects(eg. Spring in Java)
  * You just tell the framework your configuration (what objects to manage, which object needs(plugs into) which object...)
  * Have a needed object supplied as a parameter of constructor, setter etc. in your objects as instructed by the framework's manual so that needed objects are pluggable(injectable) into your objects rather than hard-coded.
  * And let the framework  (NOT you) control the creating/managing/plugging of objects (Inversion Of Control, aka. IoC)

```Java
// The following @Repository annotation indicates that a PostRepository object will be created and managed by Spring framework and ready to be plugged into objects that need one(see `PostService`).
@Repository 
public class PostRepository {
    private final Map<PostId, Post> posts;

    ...
```

```Java
// The following @Service annotation indicates that a PostService object will be created and managed by Spring framework and ready to be plugged into objects that need one.
@Service
public class PostService {
    private final PostRepository postRepository;

    // A needed object(postRepository object) is supplied by Spring framework as a parameter of constructor 
    public PostService(PostRepository postRepository) {
        this.postRepository = postRepository;
    }
```

### Dependency Injection in Spring

* Objects managed by Spring are called beans
* Bean configurations are provided in java annotations or XML files.
Annotations are more common nowadays.
* Beans can be created via:
  * `@Bean` annotations inside java config(`@Configuration`)
  * `@Component` annotations for classes and `@ComponentScan`
    * `@Controller`, `@Repository`, `@Service`, `@Configuration` include `@Component`
    * `@SpringBootApplication` includes `@Configuration` and `@ComponentScan`

```Java
@Configuration
public class DemoApplication {
  public static void main(String[] args) {
    ApplicationContext context =
      new AnnotationConfigApplicationContext(DemoApplication.class);

    System.out.println("-".repeat(80));

    PostController controller = context.getBean("postController", PostController.class);
    System.out.println(controller);

    ObjectMapper objectMapper = context.getBean("objectMapper", ObjectMapper.class);
    System.out.println(objectMapper);
  }

  @Bean
  public PostController postController() {
    System.out.println("Create bean: postController");
    return new PostController(objectMapper());
  }

  @Bean
  public ObjectMapper objectMapper() {
    System.out.println("Create bean: objectMapper");
    return new ObjectMapper();
  }
}
```
