# Netcat

Low level send and receive TCP/UDP data.

## Versions

In Ubuntu 12.04, `netcat` and `nc` are both symlinks to `nc.openbsd`.

There is also the `netcat-traditional` package which offers another version (TODO is it the GNU `netcat`?)

<http://superuser.com/questions/324812/versions-of-netcat>

This tutorial considers the BSD version by default.

## nc

Executable name.

## Basic usage

Make a TCP HTTP get request and print the response:

    printf 'GET / HTTP/1.1\r\nHost: example.com\r\n\r\n' | nc example.com 80

## Multiple requests

`nc` sends lines as you type them and over a single TCP connection if the server feels like taking it (and it should on HTTP 1.1):

    (
      printf 'GET / HTTP/1.1\r\nHost: example.com\r\n\r\n';
      sleep 2;
      printf 'GET / HTTP/1.1\r\nHost: example.com\r\n\r\n';
    ) | nc example.com 80 | grep HTTP

returns 2 responses. The same could be done by manually typing the requests in.

## u

## UDP

UDP instead of TCP.

## l

Listen for requests made on a port.

Send response from stdin.

Good way to test tools that send requests like `curl`.

Example:

    printf 'HTTP/1.1 200 OK\r\nContent-Type: text/plain\r\n\r\nHello curl!\n' \
      | nc -kl localhost 8000

On another terminal:

    curl localhost:8000

The `nc` terminal prints its input:

    GET / HTTP/1.1
    User-Agent: curl/7.22.0 (i686-pc-linux-gnu) libcurl/7.22.0 OpenSSL/1.0.1 zlib/1.2.3.4 libidn/1.23 librtmp/2.3
    Host: localhost:8000
    Accept: */*

And curl will print the reply it got: `Hello curl!`.

Same with another `nc` instead of curl:

    echo 'abc' | nc localhost 8000

### ECHO server

To do multiple tests of what is being sent, just wrap in a while and give an empty reply:

    while true; do printf '' | nc -l localhost 8000; done

If `-l` is given, then the hostname is optional. If the hostname is not given, `nc` listens on all interfaces (TODO confirm).

More advanced one that does multiple connections: <http://stackoverflow.com/questions/8375860/echo-server-with-bash>

## v

Give more verbose output.

E.g., on `nc -l`, prints an extra line:

    Connection from 127.0.0.1 port 8000 [tcp/*] accepted

before the request.

## k

Keep listing after the first connection instead of shutting down.

Requires the option `-l`.

Terminal 1:

    nc -kl localhost 8000

Terminal 2:

    echo 'abc' | nc localhost 8000
    echo 'def' | nc localhost 8000

Terminal 1 has printed:

    abc
    def

## C

If the last character is a newline `\n`, replace it with CRLF.

## HTTPS

Not possible with `nc`:

    printf 'GET / HTTP/1.0\r\n\r\n' | nc google.com 443

Returns empty.

Consider `openssl` or `ncat` <http://superuser.com/questions/346958/can-the-telnet-netcat-client-communicate-over-ssl>

## ncat

`nc` version from `nmap` package.

### c

Construct response with command.

### HTTPS

### ssl

    printf 'GET / HTTP/1.1\r\nHost: github.com\r\n\r\n' | ncat --ssl github.com 443

As of Dec 2014, Facebook is annoying and requires a known user agent, or else you will get redirected to `/unsupportedbrowser`

    printf 'GET / HTTP/1.1\r\nHost: www.facebook.com\r\nUser-Agent: Mozilla/5.0\r\n\r\n' \
    | ncat --ssl www.facebook.com 443

The `Host` is mandatory or you get a redirect. TODO why
