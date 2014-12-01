# CGI

TODO where is it specified? <http://www.w3.org/CGI/> says it's not maintained anymore.

Protocol of how a server communicates with a CGI script.

A CGI script is simply a script/executable that outputs the part of HTTP response.

This part includes some last header lines which the server delegates to it, notably `content type`, followed by `\n\n`, followed by the entire body.

The server passes information to the script through environment variables only.
