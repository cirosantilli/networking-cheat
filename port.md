# Port

Once you have determined a host (computer), e.g. through an IP or hostname, you still have to talk to one of the specific programs running on that computer.

Each program listens on an specific port which is set by convention.

Ports from 1 - 1023 are also known as "well-known ports" or "privileged ports". On UNIX-like systems, only privileged users (`root`) can bind to those ports. All have reserved or standardized functions by an organization called IANA: <http://en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers>.

The ports form 1024 to 49151 are the so called "registered ports". Projects can make a request to IANA to register one of those posts as used in order to avoid port clashes. On most systems, it is possible to bind to those ports without `sudo`.

There are 2 ports number 10: `10/tcp` and `10/udp`, each for a different protocol.

On POSIX systems, ports are typically implemented via sockets.

Ports are stored on the transport layer header, e.g. TCP source and destination port fields.

## Tools

One convenient way to write and read to ports is Netcat.

`netstat` is a great way to get information on which ports are being used by which program.
