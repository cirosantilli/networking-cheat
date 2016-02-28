# URI

# URL

# URN

Specified in both:

- <http://tools.ietf.org/html/rfc3986>, 2005.
- <http://url.spec.whatwg.org/>, used by HTML5.

URLs and URNs are URIs.

URN: uniquely identify a content, but not it's location. Unique across space and time. E.g.: ISBNs. In theory, they will never change, and will forever identify a book. However, they do not tell you where to get the book.

URLs: uniquely identify a location, but not it's content. Starts with the protocol used to get it. E.g., in `http://example.com`, `http` is the protocol. Do *not* identify the content: next year someone else could buy the domain and put up a new content.

Technically speaking, a "relative URL" like `a/b/c.html` is not an URL as the information it contains does not uniquely give the path of a resource: you also need to know the current URL for that. They are however URIs.

## Syntax

URIs have a fixed syntax, and since URLs and URNs are URIs they must also follow it.

An URL with all the possible fields is:

    http : //username:password@example.com:80/path/a ?a=b #id
    ^^^^   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ ^^^  ^^^
    1      2                                         3    4

1. scheme
2. hierarchical part
3. query (optional)
4. fragment (optional)

And the hierarchical part is composed of:

    //username:password@example.com:80 /path/a
    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ ^^^^^^^
    1                                  2

1. authority
2. path

If the hierarchical part does not start with `//`, it can contain only the path part without forward slash.

## Scheme

Says what type of resource the URI is.

Examples of schemes:

    http://example.com
    ^^^^

    javascript:void(0)
    ^^^^

In most cases for URLs, and notably HTTP, it coincides with the name of the protocol used to retrieve the resource, but there are many schemes that do not map to protocols, specially for non URLs. Some cool ones:

-   `about:`: browser info. In particular, `about:blank` is widely implemented and shows an empty document. Firefox has easter eggs at [about:robots](about:robots) and [about:mozilla](about:mozilla).

-   `chrome:`: browser configuration, also adopted on Firefox

-   `file:`: local file

-   `data:`: contains the output directly with some encoding.

    [Example](data:text/html;charset=utf-8,<p>Hello <b>world</b>.</p>).

    Sometimes used for images.

    Could also be used to transfer small code snippets when testing HTML related things, like a bug report on Firefox: <https://bugzilla.mozilla.org/show_bug.cgi?id=1189701>

-   `geo`: latitude longitude `geo:37.786971,-122.399677`

-   `javascript:`: execute JavaScript and the content is the output. Unofficial.

-   `mailto:`: send emails

-   `skype:`: Skype calls

-   [view-source:http://google.com](view-source:http://google.com): show source of URL instead of interpreting it

-   `wyciwyg`: <http://en.wikipedia.org/wiki/WYCIWYG>. Shown on cached pages.

Schemes should be registered with IANA, but there are some used in practice which are not, e.g. `isbn:`.

### Valid characters

First, depends on which part of the URL you are on: e.g. question mark `?` is valid on the fragment but not on the path.

<http://stackoverflow.com/a/25570235/895245>

Any character not in the allowed list has to be percent encoded as:

    %AB%CD%EF

where `ABCDEF` is it's UTF8 representation.

### IRI

Internationalized resource identifier. URIs that contain Unicode.

<http://en.wikipedia.org/wiki/Internationalized_resource_identifier>

<http://tools.ietf.org/html/rfc3987>

Seems to be allowed in HTML5.

## URL without protocol

## Scheme relative URL

E.g.: `//google.com` instead of `http://google.com`.

Specified with the rest of the URI specs:

- <http://tools.ietf.org/html/rfc3986#section-4.2>
- <http://url.spec.whatwg.org/#concept-scheme-relative-url>

Browser uses the same protocol as the current one.

Useful to be dry when using HTTPS vs HTTP for resources that can be retrieved in both forms.

CDNs often offer CSS and JavaScript in protocol relative URLs.

Drawback: if you open your files with the `file://` protocol, they will keep using the `file` protocol and fail for files from CDNs. Workaround: use  a web server on `http://localhost`.

Well implemented on modern browsers.

## Case

Domain names are case insensitive according to: <http://tools.ietf.org/html/rfc4343>

The rest of the URL is passed on to the server, which does whatever it wants with the data. In particular, Linux filenames are case sensitive, so a server that maps filesystem to URLs on Linux must have case sensitive URLs.

## Normalization

Great summary of things that can or cannot be normalized: <https://en.wikipedia.org/wiki/URL_normalization>

Things that cannot be safely normalized according to standards, but which people may still want to do:

- order of query parameters
