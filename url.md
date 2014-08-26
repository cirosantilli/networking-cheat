# URI

# URL

# URN

Specified in:

- <http://tools.ietf.org/html/rfc3986>, 2005.
- <http://url.spec.whatwg.org/>

URLs and URNs are URIs.

URN: uniquely identify a content, but not it's location. Unique across space and time. E.g.: ISBNs. In theory, they will never change, and will forever identify a book. However, they do not tell you where to get the book.

URLs: uniquely identify a location, but not it's content. Starts with the protocol used to get it. E.g., in `http://example.com`, `http` is the protocol. Do *not* identify the content: next year someone else could buy the domain and put up a new content.

Technically speaking, a "relative URL" like `a/b/c.html` is not an URL as the information it contains does not uniquely give the path of a resource: you also need to know the current URL for that. They are however URIs.

## Syntax

URIs have a fixed syntax, and since URLs and URNs are URIs they must also follow it.

## Scheme

<http://en.wikipedia.org/wiki/URI_scheme>

Says what type of resource the URI is.

Examples of schemes:

    http://example.com
    ^^^^

    javascript:void(0)
    ^^^^

In most cases for URLs, and notably HTTP, it coincides with the name of the protocol used to retrieve the resource, but there are many schemes that do not map to protocols, specially for non URLs. Some cool ones:

- `about:`:      browser info. In particular, `about:blank` is widely implemented and shows an empty document. Firefox has easter eggs at [about:robots](about:robots) and [about:mozilla](about:mozilla).
- `chrome:`:     browser configuration, also adopted on Firefox
- `file:`:       local file
- `data:`:       contains the output directly with some encoding
- `geo`:         latitude longitude `geo:37.786971,-122.399677`
- `javascript:`: execute Javascript and the content is the output. Unofficial.
- `mailto:`:     send emails
- `skype:`:      Skype calls
- [view-source:http://google.com](view-source:http://google.com): show source of URL instead of interpreting it
- `wyciwyg`:     <http://en.wikipedia.org/wiki/WYCIWYG>. Shown on cached pages.

Schemes should be registered with IANA, but there are some used in practice which are not, e.g. `isbn:`.
