# Networking Cheat

Networking information and cheatsheets.

Includes both protocols and utilities.

Almost all OS specifics currently only discuss Linux. Additions of info for other OSs are welcome.

Search for key terms in headings with:

    git grep -i '# http'

## TOC

- [Standards](standards.md): what are the main standards and organizations. Should be the first thing you learn.

Protocols, concepts and closely related tools:

- [ARP](arp.md)
- [DHPC](dhpc.md): DHPC, static IPs
- [Hostname](hostname.md)
- [DNS](dns.md)
- [HTTP](http.md)
- [IP](ip.md): IPv4, IPv6, IGMP
- [Jargon](jargon.md)
- [Port](port.md)
- [Proxy server](proxy-server.md)
- [REST](rest.md): restful API best practices
- [TCP](tcp.md): TCP, UDP
- [TLS](tls.md): TLS, SSL
- [URL](url.md): URL, URI

Tools:

- [cURL](curl.md)
- [dig](dig.md)
- [ifconfig](ifconfig.md)
- [iptables](iptables.md)
- [nmap](nmap.md)
- [Netcat](netcat.md)
- [netstat](netstat.md)
- [network-manager](work-manager.md): NetworkManager
- [OpenSSL](openssl.md)
- [ping](ping.md)
- [ssh](ssh.md)
- [wget](wget.md)

Servers:

- [Apache](apache.md)
- [Nginx](nginx.md)
- [Squid](squid.md)

Sniffers:

- [TShark](tshark.md)
- [tcpdump](tcpdump.md)
- [tcpflow](tcpflow.md)
- [Wireshark](wireshark.md)

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
