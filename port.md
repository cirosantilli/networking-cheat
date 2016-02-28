# Port

Once you have determined a host (computer), e.g. through an IP or hostname, you still have to talk to one of the specific programs running on that computer.

Each program listens on an specific port which is set by convention.

Ports from 1 - 1023 are also known as "well-known ports" or "privileged ports". On UNIX-like systems, only privileged users (`root`) can bind to those ports. All have reserved or standardized functions by an organization called IANA: <http://en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers>.

The ports form 1024 to 49151 are the so called "registered ports". Projects can make a request to IANA to register one of those posts as used in order to avoid port clashes. On most systems, it is possible to bind to those ports without `sudo`.

There are 2 ports number 10: `10/tcp` and `10/udp`, each for a different protocol.

On POSIX systems, ports are typically implemented via sockets.

Ports are stored on the transport layer header, e.g. TCP source and destination port fields.

## Source ports

## Local port

## Dynamic port

## Ephemeral port

<http://en.wikipedia.org/wiki/Ephemeral_port>

Every connection is made between two ports.

Therefore the client, besides knowing the correct port on the server for the desired service, must also create a local port.

If the transaction involves a response like HTTP, it is form that port that the response will be listened.

Unlike the ports on the server, which need to be well known numbers, the local ports created for a given connection can be anything, and are randomly picked on an interval of high numbers so a not to conflict with server ports that might be used locally.

On Linux, you can find out the local port range with:

    cat /proc/sys/net/ipv4/ip_local_port_range

Sample output:

    32768	61000

IANA recommends the range 49152 to 65535 (`2^15 + 2^14` to `2^16-1`) for ephemeral ports: <http://tools.ietf.org/html/rfc6335#section-6>

## Tools

One convenient way to write and read to ports is Netcat.

`netstat` is a great way to get information on which ports are being used by which program.

## Important ports

- `22`: SSH
- `53`: DNS
- `80`: HTTP
- `443`: HTTPS

### Port 0

Linux choses the first free port it can find.
