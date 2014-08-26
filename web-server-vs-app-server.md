# Web server vs app server

It is hard to distinguish them.

Generally, web server only speaks HTTP and serves static pages.

An app server, reads the HTTP, and then decides to pass the request on to a programming language like Ruby or Python if it cannot deal with it himself through an interface such as CGI.

In most production environments, the server knows which files it can serve directly without going through a script, making thing faster.

Applications like Apache and Nginx do both.
