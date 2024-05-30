# File Upload

## File Upload from html

* use `<input type="file"`
* use `<form enctype="multipart/form-data"`

```html
<form
  action="http://localhost:8080/images"
  method="POST"
  encType="multipart/form-data"
>
  <p>
    <label for="input-file">File:</label>
    <input id="input-file" type="file" name="image" />
  </p>
  <p>
    <button type="submit">
      Submit
    </button>
  </p>
</form>
```

## How to handle `multipart/form-data` request

* set `consumes` attribute
  * `@PostMapping(consumes = MediaType.MULTIPART_FORM_DATA_VALUE)`

* use `@ModelAttribute` annotation

```java
    @PostMapping(consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
    @ResponseStatus(HttpStatus.CREATED)
    public String create(
            @ModelAttribute UploadImageDto imageDto
    ) throws IOException {
        MultipartFile multipartFile = imageDto.image();

        if (multipartFile == null || multipartFile.isEmpty()) {
            return "No image";
        }

        return imageStorage.save(multipartFile.getBytes());
    }
```

```java
@Component
public class ImageStorage {
    public void save(byte[] bytes) {
        String id = TSID.Factory.getTsid().toString();

        File file = new File("data/" + id + ".jpg");

        try (OutputStream outputStream = new FileOutputStream(file)) {
            outputStream.write(bytes);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```

## How to mock MultipartFile for tests

* use `MockMultipartFile` and `MockMvcRequestBuilders.multipart`

```java
@WebMvcTest(ImageController.class)
class ImageControllerTest {
    @Autowired
    private MockMvc mockMvc;

    @Test
    @DisplayName("POST /images")
    void create() throws Exception {
        String filename = "src/test/resources/files/test.jpg";

        MockMultipartFile file = new MockMultipartFile(
                "image", "test.jpg", "image/jpeg",
                new FileInputStream(filename));

        mockMvc.perform(multipart("/images")
                        .file(file))
                .andExpect(status().isCreated());

        Mockito.verify(imageStorage).save(any());
    }
}
```

## How to use environment variable in Spring

* `application.yml` with a default value

```yaml
cloudinary:
  url: ${CLOUDINARY_URL:cloudinary://key:secret@cloud}
```

* use the value with `@Value("${cloudinary.url}")`

```java
@Component
public class ImageStorage {
    private final Cloudinary cloudinary;

    public ImageStorage(
            @Value("${cloudinary.url}") String cloudinaryUrl
    ) {
        cloudinary = new Cloudinary(cloudinaryUrl);
        cloudinary.config.secure = true;
    }

    public String save(byte[] bytes) {
        String id = TSID.Factory.getTsid().toString();

        Map options = ObjectUtils.asMap(
                "public_id", "test/" + id
        );

        try {
            Map result = cloudinary.uploader().upload(bytes, options);
            return result.get("secure_url").toString();
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```
