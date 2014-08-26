# Networking Tutorial

Information on networking protocols and applications. E.g.: TCP/IP, email, Firefox, netstat, etc.

Almost all OS specifics currently only discuss Linux. Additions of info for other OSs are welcome.

This file is a big dump of stuff that is too small to fit anywhere else.

- [standards.md](standards.md): what are the main standards and organizations. Should be the first thing you learn.

Protocols, concepts and closely related tools:

- [arp.md](arp.md)
- [dhpc.md](dhpc.md):         DHPC, static IPs
- [hostname.md](hostname.md): DNS, domains
- [http.md](http.md)
- [ip.md](ip.md):             IPv4, IPv6, IGMP
- [jargon.md](jargon.md)
- [port.md](port.md)
- [rest.md](rest.md):         restful API best practices
- [tcp.md](tcp.md):           TCP, UDP
- [tls.md](tls.md):           TLS, SSL
- [url.md](url.md):           URL, URI

Tools:

- [apache.md](apache.md):                Apache configuration
- [curl.md](curl.md)
- [ifconfig.md](ifconfig.md)
- [nmap.md](nmap.md)
- [netcat.md](netcat.md)
- [netstat.md](netstat.md)
- [network-manager.md](work-manager.md): NetworkManager
- [ping.md](ping.md)
- [ssh.md](ssh.md)
- [wget.md](wget.md)

Sniffers:

- [tcpdump.md](tcpdump.md)
- [tcpflow.md](tcpflow.md)
- [wireshark.md](wireshark.md)

You can also search for key terms in headings with commands like:

    git grep '# http'

## Sources

### Free

- <http://www.aboutdebian.com/network.htm>

### Non-free

-   [stevens - 2012 - tcp ip illustrate volume 1 2nd edition][ste12]

    Extremely clear and exemplified.

    Gives due emphasis to the key points.

    Great first book.

-   [dostalek - 2006 - Understanding tcp ip][dos06]

    Explains the most important Internet protocol suite protocols deeply.

    Might not be the best first TCP IP book because it is a bit advanced, but it is a very good second one once you know the basics.

[dos06]: http://www.amazon.com/Understanding-TCP-IP-ebook/dp/B007TUYE0G/
[ste12]: http://www.amazon.com/TCP-Illustrated-Volume-Addison-Wesley-Professional/dp/0321336313
