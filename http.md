# HTTP

HTTP is the protocol which browsers use to request pages from servers.

HTTP is part of the application layer.

The port is 80/TCP.

It is the main way that applications talk to servers.

HTTP does not find a server: it only determines exactly which characters must be passed to a server to get the data what one wants.

A major application is to request that a server send a web page. Such requests are made by browsers whenever you open a web page.

There are however many other applications outside browsers: any program can send an HTTP request. One important example are REST interfaces, which allows programs to talk to servers. One example of a REST interface is the GitHub API <https://developer.github.com/v3>, which allows programs to do anything that can be done through the browser on GitHub. This can be used to create applications that interact with GitHub's data such as third party analytics tools like <http://osrc.dfm.io/cirosantilli/>.

You can use the `nc` utility to both send and receive low level HTTP requests to understand what is going on.

## Standards

HTTP is specified by IETF.

There are currently two main versions HTTP/1.0 and HTTP/1.1 which is the most popular one today.

The HTTP/1.1 specification can be found at RFC 2616 <http://tools.ietf.org/html/rfc2616>, <http://www.w3.org/Protocols/rfc2616/rfc2616.html> (1999).

Many modifications have since been made through other RFCs:

- <https://tools.ietf.org/html/rfc4918> extensions, like 422.
- PATCH method <https://tools.ietf.org/html/rfc5789>
- `multipart/form-data` `content-type` <http://www.ietf.org/rfc/rfc2388>

## Example

TODO simple concrete example to tie in all concepts for a beginner.

A sample GET request that a browser can send to a server looks like:

    TODO

A sample POST request that a browser can send to a server looks like:

    TODO

## Minimal HTTP request

<http://serverfault.com/questions/163511/what-is-the-mandatory-information-a-http-request-header-must-contain>

    GET / HTTP/1.1
    Host: example.com

Or more precisely:

    printf 'GET / HTTP/1.1\r\nHost: example.com\r\n\r\n' | nc example.com 80

## Transport

By far the most common is TCP, not UDP: <http://stackoverflow.com/questions/323351/does-http-use-udp>

Quoting the spec:

> This does not preclude HTTP from being implemented on top of any other protocol on the Internet, or on other networks. HTTP only presumes a reliable transport

Since UDP is not "reliable transport", it should not be used.

HTTP is a synchronous request-response protocol <https://en.wikipedia.org/wiki/Request-response>. A simplified transaction goes like this:

- client opens the connection with 3-way handshake
- client sends the request
- server ACKs the request
- server send the reply
- client ACKs the reply
- client makes further requests (images, CSS, etc.) until satisfied, and server replies
- client starts the closing handshake

To observe a transaction in detail, use Wireshark:

- set a Wireshark display filter by address `ip.host == example.com`
- `curl example.com`

It says that the connection went like this:

    192.168.0.10    example.com      TCP     47996 > http [SYN] Seq=0
    example.com     192.168.0.10     TCP     http > 47996 [SYN, ACK] Seq=0 Ack=1
    192.168.0.10    example.com      TCP     47996 > http [ACK] Seq=1 Ack=1
    192.168.0.10    example.com      HTTP    GET / HTTP/1.1
    example.com     192.168.0.10     TCP     http > 47996 [ACK] Seq=1 Ack=76
    example.com     192.168.0.10     HTTP    HTTP/1.1 200 OK  (text/html)
    192.168.0.10    example.com      TCP     47996 > http [ACK] Seq=76 Ack=1592
    192.168.0.10    example.com      TCP     47996 > http [FIN, ACK] Seq=76 Ack=1592
    example.com     192.168.0.10     TCP     http > 47996 [FIN, ACK] Seq=1592 Ack=77
    192.168.0.10    example.com      TCP     47996 > http [ACK] Seq=77 Ack=1593

### Connection header

### Keep-Alive

### Persistent connection

In HTTP 1.1, multiple HTTP requests are done by default on a single connection, since almost all pages require the loading of multiple resources from a single server: image, CSS, etc. In `HTTP 1.0`, this had to be indicated through the `Connection: Keep-Alive` header, which most browsers still send as of 2014.

Demo:

Two replies, not mandatory on 1.1:

    (
      printf 'GET / HTTP/1.1\r\nHost: example.com\r\n\r\n';
      printf 'GET / HTTP/1.1\r\nHost: example.com\r\n\r\n';
    ) | nc example.com 80 | grep HTTP

Output:

    HTTP/1.1 200 OK
    HTTP/1.1 200 OK

And Wireshark tells me the connection went like this:

    192.168.0.10    example.com     TCP      47741 > http [SYN] Seq=0
    example.com     192.168.0.10    TCP      http > 47741 [SYN, ACK] Seq=0 Ack=1
    192.168.0.10    example.com     TCP      47741 > http [ACK] Seq=1 Ack=1
    192.168.0.10    example.com     HTTP     GET / HTTP/1.1 GET / HTTP/1.1
    192.168.0.10    example.com     TCP      47741 > http [FIN, ACK] Seq=75 Ack=1
    example.com     192.168.0.10    TCP      http > 47741 [ACK] Seq=1 Ack=75
    example.com     192.168.0.10    HTTP     HTTP/1.1 200 OK  (text/html)
    192.168.0.10    example.com     TCP      47741 > http [ACK] Seq=76 Ack=1592
    example.com     192.168.0.10    HTTP     HTTP/1.1 200 OK  (text/html)
    192.168.0.10    example.com     TCP      47741 > http [ACK] Seq=76 Ack=3183
    example.com     192.168.0.10    TCP      http > 47741 [FIN, ACK] Seq=3183 Ack=76
    192.168.0.10    example.com     TCP      47741 > http [ACK] Seq=76 Ack=3184

so single `SYN` and thus single connection.

On HTTP 1.0, `Keep-Alive` is mandatory:

    (
      printf 'GET / HTTP/1.0\r\nHost: example.com\r\n\r\n';
      printf 'GET / HTTP/1.0\r\nHost: example.com\r\n\r\n';
    ) | nc example.com 80 | grep HTTP

Output:

    HTTP/1.1 200 OK

Two replies:

    (
      printf 'GET / HTTP/1.0\r\nHost: example.com\r\nConnection: keep-alive\r\n\r\n';
      printf 'GET / HTTP/1.0\r\nHost: example.com\r\nConnection: keep-alive\r\n\r\n';
    ) | nc example.com 80 | grep HTTP

Output:

    HTTP/1.1 200 OK
    HTTP/1.1 200 OK

## Newlines

Every newline is a CRLF. Tools such as `curl` convert `\n` to `\r\n`.

## First line

The first line is different for requests and responses.

### Initial request line

An initial request line looks something like:

    GET /path/to/file.html HTTP/1.0
    1   2                  3

Or:

    POST /path/to/resource HTTP/1.1
    1    2                 3

Where:

1. method
2. path
3. HTTP version

### Initial response line

    HTTP/1.0 200 OK
    ^        ^   ^
    1        2   3

1.  protocol and version

2.  status code. Programmatic use, so get it right.

3.  Reason Phrase. Each status code has a default reason phrase,
    but any phrase may be used, the value is not fixed by the protocol.

#### Method

Determines in general terms what the request is about.

RFC 2616 specifies the following methods:

- `GET`: get information from server

- `HEAD`: only get header information from server

- `POST`: send data to server to create new objects. E.g.: you click on the submit button of an HTML `form` with `method="post"`.

- `PUT`: update or create entire objects on server. Both PUT and POST can be used to create object, but `PUT` is *idempotent*.

- `DELETE`: remove objects from server. Idempotent.

- `TRACE`: TODO

- `CONNECT`: TODO

There are also proposed methods in other RFCs:

-   `PATCH` in RFC 5789 <https://tools.ietf.org/html/rfc5789>

    Update object on server.

    Vs `PUT`: only attributes of the objects which are sent are modified.

    In `PUT`, attributes not given are set to default values.

    In `PATCH`, attributes not given are not modified.

##### Safe Methods

Methods that shouldn't change the server state significantly. E.g.: `GET`, `HEAD`.

In practice however, many `GET` requests do change the server state: a common example being page view count. However this operation is not considered drastic, and it is generally accepted to be done via `GET`.

Use safe methods whenever possible.

##### Idempotent Method

If a request is sent twice with an idempotent method, it should have the same effect as if it were sent once.

E.g.: `PUT`, `DELETE` and all safe methods.

Both `PUT` and `POST` can be used for object creation: the difference often comes down to: does the user control the unique ID that identifies the created object, often the one shown on the URL to the object? Or is it the server that automatically generates the ID? If the user controls it, a double request is idempotent because the second would be an update with the same data as the first, so `PUT` is the best choice.

### Status line

An initial response line looks something like:

    HTTP/1.0 200 OK
    1        2   3

Or:

    HTTP/1.1 404 Not Found
    1        2   3

Where:

1. HTTP version
2. status code
3. status code name. There is only one possible name for every status code.

#### Status code

All status codes can be found here: <http://en.wikipedia.org/wiki/List_of_HTTP_status_codes>

Some of the more interesting ones are commented here.

##### Redirect

##### 3xx

Very readable spec: <http://tools.ietf.org/html/rfc2616#section-10.3>

Redirect to another page for a given reason.

All of those statuses require the `Location` header that indicates where to redirect to.

The spec recommends that an HTML redirect page be given in the body in case the user agent does not follow redirects. Most browsers do so by default, and don't ever show Moved pages.

    HTTP/1.1 301 Moved Permanently
    Location: http://www.example.org/
    Content-Type: text/html
    Content-Length: 174

    <html>
    <head>
    <title>Moved</title>
    </head>
    <body>
      <h1>Moved</h1>
      <p>This page has moved to <a href="http://www.example.org/">http://www.example.org/</a>.</p>
    </body>
    </html>

By default, the new request should not change the initial method, unless stated otherwise as in 303.

If the new method would not be a `GET` or `HEAD` request, the UA can only carry it out if it is possible to ask for confirmation from the user, since something like `POST` may have unwanted consequences.

User agents should detect infinite redirect loops.

###### Post Redirect Get pattern

<http://en.wikipedia.org/wiki/Post/Redirect/Get>

###### 301

Permanent redirect: the page has been moved forever.

Next time, UAs can cache this redirect, and may not even bother sending a request to the first location.

Only works for `GET` requests.

TODO how to revert this if you change your mind later? <http://getluky.net/2010/12/14/301-redirects-cannot-be-undon/> suggests it is impossible.

###### 302

Hell.

In the spec, exact same as 307.

In practice, implemented exactly as 303, even on 2014 browsers, and nothing can be done to change that without breaking things.

Still the most common response promoted by 2014 web frameworks. E.g., Rail's most common redirection method `redirect_to` uses it. TODO why? Compatibility with HTTP 1.0 which does not have 303 and 307, even if 1.1 is very widely implemented?

###### 303

Redirect to Location, and use `GET` whatever the first method was.

Introduced in HTTP 1.1 do disambiguate 302. Counterpart of 307.

In practice, this is what all 302 implementations do.

This is the most correct response after an user submitted a POST form and you want him to see the object he created, although in practice 302 still is more common.

Perhaps someday we will be able to use XHR and Javascript `pushState` for form submissions, thus saving one extra request.

###### 304

Not modified.

Application:

The user pressed the refresh key on an open browser page.

It would be wasteful to refecth the current page if it was not modified since it last fetched. `304` exists to avoid just that.

If the client already has an older version of the resource cached, it can send in the request one of the fields `If-Modified-Since` or `If-Match` containing the date at which the resource was obtained.

The server sees if the resource has been updated since that date, and if not can return a 304.

###### 307

In the spec, exact same as 302.

Introduced in HTTP 1.1 with 303 because all browsers treat 302 as 303, and nothing can be done to change it now.

This time we hope browsers will follow the spec.

##### 4xx

###### 400

Bad syntax. E.g., malformed JSON.

In theory, *not* for invalid values, e.g., invalid email address. Use 422 for that if supported.

###### 401

Server should include a `WWW-Authenticate` field specifying what kind of authentication is required.

Try this with:

    curl -I localhost/location/that/requires/auth

I get for example:

    WWW-Authenticate: Basic realm="AuthName value"

so the type is Basic

`AuthName value` is a any descriptive string set by the server operators. In Apache it is given by the `AuthName` directive.

###### 403

Vs. 401: <http://stackoverflow.com/questions/3297048/403-forbidden-vs-401-unauthorized-http-responses>

###### 422

WebDAV extension: <https://tools.ietf.org/html/rfc4918#section-11.2>

Returned for invalid parameters sent, e.g., invalid email address sent.

Returned by Rails 4 on form validation.

## Headers

Certain headers can only be used on requests, other only on responses, and a few on both.

Headers are case insensitive. A common style is to capitalize the first letter of each word.

### Headers that can be used on both request and response

#### Content-Type

The MIME type of the data being sent on the body.

Large list with simple explanations: <http://en.wikipedia.org/wiki/Internet_media_type#Type_text>

-   `text/html`: HTML document. Browsers interpret body as HTML and renders it.

-   `application/xhtml+xml`: XHTML.

-   `text/plain`: browser pastes to screen, no HTML rendering. So you will see tags like `<h1>` on screen.

-   `application/x-www-form-urlencoded`: key value pairs in the same format that can be given on an URL.

    The default `content-type` for `method="post"` on HTML forms.

    The `content-type` can be modified via the `enctype` `form` attribute.

-   `application/json`: popular choice for rest APIs. Used on the GitHub API.

-   `text/css`

-   `application/pdf`

-   `application/javascript`

-   `application/octet-stream`: TODO

##### multipart/form-data

Specified at RFC 2388 <http://www.ietf.org/rfc/rfc2388>.

Encapsulates multiple header / body pairs into one body HTTP body.

Advantage of `multipart/form-data`:

-   Huge memory savings for binary files, in which:

    - many bytes would have to be URL encoded for `application/x-www-form-urlencoded` (3 bytes per encoded byte).

    - the size would be 33% larger with `application/x-www-form-urlencoded` + `base64` encode.

Disadvantages of `multipart/form-data`:

-   Each field has a data overhead for the boundary and the sub headers.

    This is easily overcome by memory gains of using it if there is a file.

Therefore: use it iff upload of a binary file is possible on the request.

Sample request: <http://stackoverflow.com/a/23518189/895245>

##### boundary

This is the only type of request does not have an empty line after the headers: `boundary` comes directly.

`boundary` specifies a sequence of bytes which separates each of the bodies.

The boundary is always surrounded by CRLFs which are not part of the data.

The boundary cannot appear inside the data: the user agent must chose it appropriately.

The trailing hyphens of the boundary are often added for partial backward compatibility with older multipart RFCs, and to improve readability. TODO are they mandatory?

#### Content-Length

Length of the body in bytes.

Very important when body is present because it allows the receiver to allocate memory at once.

Was mandatory on HTTP 1.0, but on HTTP 1.1 may be replaced on `Transfer-Encoding: chunked`.

TODO mandatory if body is present?

#### Content-Encoding

Must be one of the request `Accept-Encoding` values.

-   `gzip`: the content was gzipped before sending it to the browser.

    Supported by almost all modern browsers: Firefox sends it by default, Apache has `mod_gzip` which gzips everything when possible.

### Request headers

#### Host

The only mandatory header in HTTP 1.1.

With `curl http://localhost:8000`:

    Host: localhost:8000

The port should be included if it's not the default `80`: <http://stackoverflow.com/questions/3364144/is-port-number-required-in-http-host-header-parameter>

With `curl 127.0.0.1`:

    Host: 127.0.0.1

With `curl http://google.com`:

    Host: google.com

Some hosts are very picky about the `Host` header. E.g., `example.com` give a 404 if you don't set it, or set it wrong:

    curl `dig +short example.com`

results in 404, while:

    curl example.com

works.

The purpose of this annoying mandatory header is to allow for name based virtual hosting: <http://en.wikipedia.org/wiki/Virtual_hosting>, i.e., multiple hostnames on a single IP.

TODO: how are browsers able to decide this? If you click on a link, browsers send the same `Host` as the hostname. Is it possible to change that?

#### User-Agent

Description of the user agent that sent the request.

Request counterpart of `Server`.

Firefox 29:

    User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux i686; rv:29.0) Gecko/20100101 Firefox/29.0

Curl 7.22:

    User-Agent: curl/7.22.0 (i686-pc-linux-gnu) libcurl/7.22.0 OpenSSL/1.0.1 zlib/1.2.3.4 libidn/1.23 librtmp/2.3

#### Accept

A comma separated list of MIME type that the client wants as a response.

Specified on the response by the `Content-Type` header.

It is possible that a single URL is able to return several types.

In this case this field can be used by the server to determine the type to serve.

Firefox 29:

    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8

The `q=` are the quality factors, that indicate the level of preference for each listed media type: <http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.1>

#### Accept-Language

Comma separated list of accepted `Content-Language` for the response.

Firefox 29:

    Accept-Language: en-US,en;q=0.5

#### Accept-Encoding

Comma separated list of accepted `Content-Encoding` for the response.

Firefox 29:

    Accept-Encoding: gzip, deflate

#### Connection

`keep-alive`: TODO

#### Extensions

##### Forwarded-For

<http://en.wikipedia.org/wiki/X-Forwarded-For>

Usually set by reverse proxies.

Holds the IP of the original request.

### Response headers

#### Transfer-Encoding

TODO vs `Content-Encoding`?

#### Content-Disposition

Suggests to the browser what to do to certain types of data

`attachment` suggests to the browser to download the file with given filename:

    Content-Disposition: attachment; filename=genome.jpeg;

`inline` suggest to the browser to show the content inline if it has that capability:

    Content-Disposition: inline

In Firefox, the browser preferences under `Edit > Preferences > Application` determine what to do for each MIME type, and overrides this header.

<http://i8jesus.com/?p=64> suggests that `Content-Disposition` is not sufficient protection from [unrestricted file upload](https://www.owasp.org/index.php/Unrestricted_File_Upload) XSS.

#### Server

Software that sent the response.

- `google.com`: `gws`. Google Web Server, closed source.
- `twitter.com`: `tfe`

Not mandatory: Facebook does not return it.

#### Referer

Sent by the UA containing the page from which the request was sent, so in the case of hyperlinks the page who refered the user to the new one.

Optional, so don't rely on it: <http://stackoverflow.com/questions/6023941/how-reliable-is-http-referer>

Was originally misspelled as `referrer`, with two R's on older specs, but this has been corrected in newer specs.

Used by Rails `redirect_to :back` shortcut.

#### Extensions

##### Sendfile

##### Accel

<http://wiki.nginx.org/X-accel>

<https://tn123.org/mod_xsendfile/>

If the response contains this header, the rest of the response is ignored and the file is served instead, and the server uses all of it's optimizations.

Normally this the reverse proxy is configured to consider this header only on the response of the server.

This header is useful for serving static files for which the server has to control access: there fore the request must pass through the server first. But it is inefficient to serve files from the backend server, so the backend server passes this header and delegates to the reverse proxy.

### CORS headers

The following headers are used for CORS requests and responses:

#### Origin

#### Access-Control-Allow-Origin: http://api.bob.com

#### Access-Control-Allow-Credentials: true

#### Access-Control-Expose-Headers: FooBar

### Custom headers

<http://stackoverflow.com/questions/3561381/custom-http-headers-naming-conventions>

In the past, prefix by `X-`.

After 2012: cross your fingers and pick a name.

## Body

TODO

## HTTPS

HTTP over TLS.

Encrypts both body and headers.

Downside: encrypt/decrypt costs time.

HTTPS runs on port 443 instead of 80.

## HTTP authentication

Authentication that is sent over the HTTP header.

### Sources

<http://www.httpwatch.com/httpgallery/authentication/>

Comparison to form auth, nice diagrams: <http://docs.oracle.com/javaee/1.4/tutorial/doc/Security5.html>

Great post: <http://stackoverflow.com/questions/549/the-definitive-guide-to-forms-based-website-authentication>

### Downsides of HTTP auth

Parameters are left to the browser:

- the appearance of the login page
- the time for which the user stays authenticated (time for which browser keeps resending `user:pass` automatically).

You might have seen this on a website in which your browser just opens up a weird looking window and asks you for username / password.

Therefore, you cannot customize them and users will get different interfaces on different browsers, bad user interface consistency.

For those reasons, form authentication is used on most large sites today.

### Upside of HTTP auth

Simple.

### Basic authentication

Authentication is sent on the header *unencrypted*!

Example:

    curl -vu u:p google.com

You see the header line:

    Authorization: Basic dTpw
    ^^^^^ ^^^^
    1     2

where:

- 1: authentication type
- 2: base 64 of u:p. not encryption!

Just checking:

    assert [ "`echo dTpw | base64 -d`" = "u:p" ]

#### URL convention

Many programs accept URLs strings with user/pass included:

    curl -v u:p@google.com

This is however just a convention, since programs that accept it parse the string to extract the `u:p` part, and then send it on the header.

### Digest authentication

Pretty cool concept

See: <http://en.wikipedia.org/wiki/Digest_access_authentication>

Authentication is sent on the header md5 hashed:

    curl --digest -vu u:p google.com

#### Why it works

Data is appended to the authentication with `:` before hashing:

- domain (`www.google.com`)
- method (GET, POST, etc.)
- nonce
- nonce is sent to client from server.
- *nonces can only be used once per client*!!
- nonce prevents requests from being repeated with an old captured hashed string!
- also increases the difficulty of cracking each user/pass

This way, the unknown user and pass get mixed up with the extra data in the hash and it is very hard to separate them. and the nonce makes sure requests cannot be remade by resending the hash.

Merits:

- simpler than a full SSL

### NTML

Safer than digest: replay attacks impossible.

Requires server state, so HTTP 1.1 only.

Little current support/usage.

### CSRF

### Cross site request forgery

The attacker Bob posts on a website:

    http://bank.com?transfer-ammount=10000&to=bob

Alice clicks on the disguised link, her browser sends the authentication cookies, authenticates and makes the request.

How to prevent it: <https://www.owasp.org/index.php/Cross-Site_Request_Forgery_%28CSRF%29_Prevention_Cheat_Sheet>

#### Synchronizer Token Pattern.

The most popular prevention mechanism.

Send extra randomly generated data with valid forms.

Generation can be per session or per form, which is more secure but less efficient and may have usability impact.

The method relies on the same origin policy: if it did not exist the attacker would be able to obtain the token.

This method is implemented by default in most web frameworks, either by adding a hidden form field (Django) or `meta` elements like Rails:

    <meta content="authenticity_token" name="csrf-param" />
    <meta content="asdfqwerASDFQWER12345678" name="csrf-token" />

#### Reauthenticate

Ask user for password again before critical operations.

Used in all online banking systems.

Inconvenient for users since an extra action is required before an authentic request.

## Sources

Good intro tutorial: <http://www.jmarshall.com/easy/http/#structure>
