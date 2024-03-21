# The Beginning: Hypertext Transfer Protocol (HTTP)

## What is HTTP?

MDN Web Docs' definition of HTTP is this:
> Hypertext Transfer Protocol (HTTP) is an application-layer protocol for transmitting hypermedia documents, such as HTML.

Well, we could use a word-by-word explanation for this.

## Why HTTP?

Out of this wordy definition, we first focus on the **"HTTP is a protocal for transmitting documents"** part of the definition. Then the question of 'Why HTTP?'  boils down to the question of "Why do you need a protocal for transmitting documents?".

Think of sending receiving letters between two parties living in different parts of the world. It is commonly shared rules about sending and receiving mail that make this system work in spite of differences in cultures and languages across the globe.

* Information about senders and receivers are written on the envelop.
  * The senders' information is written on the upper-left of the envelop.
  * The receivers' infromation is written on the lower-right of the envelop.
* The contents senders want to convey is inside the envelop.

Likewise, we need a shared set of rules about the transmission of documents in order to transmit documents between programs that live in different OS'es and are written in different programming languages.

## The nitty gritty

### What not to focus on

Before we get down to the nitty-gritty, let's get laser-focused on what really matters to grasp the overview of HTTP.

> Hypertext Transfer Protocol (HTTP) is an **application-layer** protocol for transmitting **hypermedia documents**, such as HTML.

Wikipedia says an application layer is an abstraction layer that specifies the shared communication protocols and interface methods used by hosts in a communications network. This is a fancy way of saying "When something is on application layer, it only concerns about communicating rules agreed upon by parties on each end." Back to our offline mailing service example, it never cares about which courier will pick up the letter, mode of transportation etc. just focusing on the rules of preparing the letter and receiving the letter agreed upon by both ends of the communication.

We will also gloss over the actual content of the communication, the actual message kept inside the envelop. A hypermedia document is another fancy word meaning a document that contains graphics, audio, video, plain text and hyperlinks.

### HTTP request message

HTTP was originally designed for communication between web browsers and web servers. Web broswers request information and web servers respond with requested information.

The following is a simple example of HTTP request.

```HTTP
GET / HTTP/1.1
Host: example.com

```

**GET**  is a type of HTTP method.

#### HTTP methods

* GET → Read data from server (idempotent)
* HEAD → GET without body
* POST → Submit to server to create data in a server (not idempotent)
* PUT → overwrite existing data with new data submitted.
* PATCH → Partially update server data (not idempotent)
* DELETE → Delete reuqested data from a server
* OPTIONS → Show methods supported by a server

**1.1** is a version of HTTP to be used for communication.

**Host: example.com** is header ,which is optional information sent to servers.

Note the blank line after the header.

### HTTP response message

```HTTP
HTTP/1.1 200 OK
Date: Sat, 09 Oct 2010 14:28:02 GMT
Server: Apache
Last-Modified: Tue, 01 Dec 2009 20:18:22 GMT
ETag: "51142bc1-7449-479b075b2891b"
Accept-Ranges: bytes
Content-Length: 29769
Content-Type: text/html

<!DOCTYPE html>… (here come the 29769 bytes of the requested web page)
```

**200** is HTTP status code

#### HTTP status code

* 1xx → Information responses ⇒ Rarely used by developers.
* 2xx → Success ⇒ 200 OK, 201 Created, 204 No Content
* 3xx → Redirection ⇒ 304 Not Modified : It tells the client that the response has not been modified, so the client can continue to use the same cached version of the response.
* 4xx → Client-side issues ⇒ 404 Not Found
* 5xx → Server-side issues ⇒ 500 Internal Server Error

There is a blank line between headers and a response body(actual content).

See [Section 3 of RFC-7230](https://datatracker.ietf.org/doc/html/rfc7230#section-3) for details on HTTP message format.
