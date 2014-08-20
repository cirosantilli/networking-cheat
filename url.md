# URI

# URL

# URN

URLs and URNs are URIs.

URN: uniquely identify a content, but not it's location. Unique across space and time. E.g.: ISBNs. In theory, they will never change, and will forever identify a book. However, they do not tell you where to get the book.

URLs: uniquely identify a location, but not it's content. Starts with the protocol used to get it. E.g., in `http://example.com`, `http` is the protocol. Do *not* identify the content: next year someone else could buy the domain and put up a new content.

Technically speaking, a "relative URL" like `a/b/c.html` is not an URL as the information it contains does not uniquely give the path of a resource: you also need to know the current URL for that. They are however URIs.
