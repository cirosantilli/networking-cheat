# Telnet

Protocol for communicating between servers and name of command line tool that implements it.

- <http://tools.ietf.org/html/rfc854>
- <http://tools.ietf.org/html/rfc855>

Very old origins: initially RFC 15 (1969)!.

No encryption, therefore *dont't send passwords on untrusted network with this*!

Always use ssh which is encrypted for anything even remotely serious.

The other computer must be running a telnet server.

Fun MUD games!.

Make HTTP requests by hand for learning purposes:

    telnet google.com 80

Type:

    GET / HTTP/1.0 <enter><enter>

You've made a get request by hand!

TODO won't work, why? How to programmatically write characters on a request?

    echo $'GET / HTTP/1.0\n\n' | telnet www.google.com 80

Also consider:

- `nc`
- `socat`
