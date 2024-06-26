# Collection pattern

## What is a Collection Pattern?

Suppose we want to design a URI system. One would go about this like:

* `/the-first-post`
* `/the-second-post`
* `/test-post`

But we can make this look more organized and readable by grouping like-resources into a group like the following:

* `/posts`
* `/posts/the-first`
* `/posts/test`
* `/posts/{id}` or `/posts/:id`

Note the plural form of the group name. (post**s**)

Although group names in the plural form are recommended, we can make them singular when there are no other like-resources.

* `/session` : There is one and only one session for a user.

## CRUD

All operations upon data belong to one of the following:

1. Create
2. Read
3. Update
4. Delete

### CQS(Command Query Separation)

CRUD can be further categorized into Command and Query:

1. Command -> Create, Update, Delete => change in state. Not Safe.
2. Query -> Read => state stays the same. Safe. Easy to distribute and cache

A method cannot be a Command AND a Query. It can be either a Command and a Query.

## CRUD Operation represented by HTTP methods and collection pattern

1. GET -> read
2. POST -> Create
3. PUT, PATCH -> Update
4. DELETE -> Delete

### Posts

1. `GET /posts`
2. `GET /posts/{id}`
3. `POST /posts`
4. `PUT or PATCH /posts/{id}`
5. `DELETE /posts/{id}`

### login/logout

1. `POST /session` -> create a session = login
2. `DELETE /session` -> destroy a session = logout
3. `GET /session` -> check a session
4. `GET /users/me`

## Simple API in collection pattern

```java
package com.example.api.rest.demo.controllers;

import com.example.api.rest.demo.exceptions.PostNotFound;
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/posts")
public class PostController {
    @GetMapping
    public String list() {
        return "The list of posts";
    }

    @GetMapping("/{id}")
    public String detail(@PathVariable String id) {
        if (id.equals("404")) {
            throw new PostNotFound();
        }
        return "Post Detail: " + id + "\n";
    }

    @ExceptionHandler(PostNotFound.class)
    @ResponseStatus(HttpStatus.NOT_FOUND)
    public String postNotFound() {
        return "Post Not Found";
    }

    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public String create(@RequestBody String body) {

        return "{\"action\": \"Create Post\", \"body\": \"" +
                body.replace("\"", "\\\"") + "\"}";
    }

    @PatchMapping("/{id}")
    public String update(@PathVariable("id") String id, @RequestBody String body) {
        return "{\"action\": \"Update Post: " + id + "\", \"body\": \"" +
                body.replace("\"", "\\\"") + "\"}";
    }

    @DeleteMapping("/{id}")
    public String delete(@PathVariable("id") String id) {
        return "Delete Post: " + id;
    }
}
```

* @RequestBody - Annotation indicating a method parameter should be bound to the body of the web request.
* @ResponseStatus - Marks a method or exception class with the status code and reason that should be returned. The status code is applied to the HTTP response when the handler method is invoked
* @ExceptionHandler - Annotation for handling exceptions in specific handler classes and/or handler methods.
