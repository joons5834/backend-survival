# Test

We program to solve business issues.
We check resolution of issues by defining them and imagining what it looks like to have isssues solved.

## Unit Testing

* Is this part reliable?
* What do you do with reliable parts?

[Example: Square Roots by Newton's Method](https://mitp-content-server.mit.edu/books/content/sectbyfn/books_pres_0/6515/sicp.zip/full-text/book/book-Z-H-4.html#%_toc_%_sec_1.1.7)

```Java
class NewtonMethodTest {
  private NewtonMethod sut;  // System Under Test

    @BeforeEach
    void setUp() {
    sut = new NewtonMethod();
    }

    @Test
    void goodEnough() {
    assertThat(sut.goodEnough(2, 4)).isTrue();
    assertThat(sut.goodEnough(1, 4)).isFalse();

    assertThat(sut.goodEnough(1.999999, 4)).isTrue();
    }

    @Test
    void improve() {
    DecimalFormat decimalFormat = new DecimalFormat("0.####");
    assertThat(decimalFormat.format(sut.improve(1, 2))).isEqualTo("1.5");
    assertThat(decimalFormat.format(sut.improve(1.5, 2))).isEqualTo("1.4166");
    assertThat(decimalFormat.format(sut.improve(1.4166, 2))).isEqualTo("1.4142");
    }

    @Test
    void sqrtIter() {
    DecimalFormat decimalFormat = new DecimalFormat("0.######");
    assertThat(decimalFormat.format(sut.sqrtIter(1, 2))).isEqualTo("1.414216");
    assertThat(decimalFormat.format(sut.sqrtIter(1, 3))).isEqualTo("1.732143");
    assertThat(decimalFormat.format(sut.sqrtIter(1, 4))).isEqualTo("2");
    }

    @Test
    void sqrt() {
    DecimalFormat decimalFormat = new DecimalFormat("0.######");
    assertThat(decimalFormat.format(sut.sqrt(2))).isEqualTo("1.414216");
    assertThat(decimalFormat.format(sut.sqrt(3))).isEqualTo("1.732143");
    assertThat(decimalFormat.format(sut.sqrt(4))).isEqualTo("2");
    }

}
```

## Spring Testing

You test with Spring to utilize IoC container or to test parts implemented in Spring Web MVC

### SpringBootTest

```java
@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
class PostFeatureTest {
  @Value("${local.server.port}")
  private int port;

  @Autowired
  private TestRestTemplate restTemplate;

  @Test
  public void post() {
    String url = "http://localhost:" + port + "/posts";

    PostDto postDto = new PostDto("ID", "new post", "제곧내");

    restTemplate.postForLocation(url, postDto);

    String body = restTemplate.getForObject(url, String.class);

    assertThat(body).contains("new post");
    assertThat(body).contains("content");

    String id = findLastId(body);

    restTemplate.delete(url + "/" + id);

    body = this.restTemplate.getForObject(url, String.class);

    assertThat(body).doesNotContain("new post");
  }

  private String findLastId(String body) {
    Pattern pattern = Pattern.compile("\"id\":\"([^\"]+)\"");
    Matcher matcher = pattern.matcher(body);

    String id = "";
    while (matcher.find()) {
      id = matcher.group(1);
    }
    return id;
  }
}
```

### MockMvc

You can use MockMvc to mock HTTP request.

```java
@SpringBootTest
@AutoConfigureMockMvc
class PostControllerTest {
  @Autowired
  private MockMvc mockMvc;

  @Autowired
  private PostRepository postRepository;

  @Test
  public void list() throws Exception {
    this.mockMvc.perform(get("/posts"))
      .andExpect(status().isOk())
      .andExpect(content().string(
        containsString("테스트입니다")
      ));
  }

  @Test
  public void create() throws Exception {
    String json = """
      {
        "title": "새 글",
        "content": "제곧내"
      }
      """;

    int oldSize = postRepository.findAll().size();

    this.mockMvc.perform(
        post("/posts")
          .contentType(MediaType.APPLICATION_JSON)
          .content(json)
      )
      .andExpect(status().isCreated());

    int newSize = postRepository.findAll().size();

    assertThat(newSize).isEqualTo(oldSize + 1);
  }
}
```

### SpyBean

* “Spies are stubs that also record some information based on how they were called.”

Just check if `save` of `postRepository` was called.

```java
@Test
public void create() throws Exception {
  String json = """
    {
      "title": "new post",
      "content": "content"
    }
    """;

  this.mockMvc.perform(
      post("/posts")
        .contentType(MediaType.APPLICATION_JSON)
        .content(json)
    )
    .andExpect(status().isCreated());

  verify(postRepository).save(any(Post.class));
}
```

Check the parameter values passed to `save`.

```java
@Test
public void create() throws Exception {
  String json = """
    {
      "title": "new post",
      "content": "content"
    }
    """;

  this.mockMvc.perform(
      post("/posts")
        .contentType(MediaType.APPLICATION_JSON)
        .content(json)
    )
    .andExpect(status().isCreated());

  verify(postRepository).save(argThat(post -> {
    return post.title().equals("new post");
  }));
}
```

### MockBean

We can use the `@MockBean` to add mock objects to the Spring application context.
It also speeds up test.

```java
@WebMvcTest(PostController.class)
class PostControllerTest {
  @Autowired
  private MockMvc mockMvc;

  @MockBean
  private GetPostsService getPostsService;

  @MockBean
  private GetPostService getPostService;

  @MockBean
  private CreatePostService createPostService;

  @MockBean
  private UpdatePostService updatePostService;

  @MockBean
  private DeletePostService deletePostService;

  @Test
  public void list() throws Exception {
    given(getPostsService.getPostDtos()).willReturn(List.of(
      new PostDto("1", "제목", "내용")
    ));

    this.mockMvc.perform(get("/posts"))
      .andExpect(status().isOk())
      .andExpect(content().string(
        containsString("제목")
      ));
  }

  @Test
  public void create() throws Exception {
    String json = """
      {
        "title": "new post",
        "content": "content"
      }
      """;

    this.mockMvc.perform(
        post("/posts")
          .contentType(MediaType.APPLICATION_JSON)
          .content(json)
      )
      .andExpect(status().isCreated());

    verify(createPostService)
      .createPost(new PostDto(null, "new post", "content"));
  }
}
```

## TDD (Test Driven Development)

TDD is a development methodology where test codes are written first before working codes are written and developers tap into what they learn from the process.

### TDD Cycle

1. Red -> Write a test code that fails
2. Green -> Have your code pass the tests as quickly as posssible
3. Refactor -> Refactor your code. The most important part of TDD that is often overlooked.
