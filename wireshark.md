# Wireshark

Set of utilities that that capture (snif) TCP packages similarly to `tcpdump`.

TODO: does Wireshark show only TCP, or also IP and link layer? It seems that it is capable of showing IP layer also, but if you set the Preferences > Protocols > IPv4 > Reassemble fragmented IPv4 datagrams (set by default), then it reassembles IPs fragments into transport layer and I'm not sure how the interface looks without this.

Wireshark displays only TCP layer datagrams, not IP or link datagrams.

Analyzes and parses TCP payload for many protocols like HTTP, and presents them differently depending on the protocol.

E.g.: if a TCP datagram contains HTTP data, it shows as:

    Protocol  Info
    HTTP      GET / HTTP/1.1

even if it is also a TCP datagram, while an ACK shows as:

    Protocol  Info
    HTTP      ACK Seq=1 Ack=1 ...

For this reason this is an amazing tool to really understand everything that goes on your computer's network interfaces.

Open source and cross platform (Linux and Windows).

## Filters

In order to get any meaningful data, it is very important to filter the packages.

You can filter by either of:

- interface
- IP
- port
- transport protocol
- application layer protocol

Wireshark can filter by several layers

You can create new custom filters from Analyse > Display filters. Some sample filter expressions:

    tcp.port == 80 || udp.port == 80

Where `tcp.port` and `udp.port` are the ports on either endpoint.

IP of any endpoint:

    ip.addr == 10.43.54.65

Same as:

    ip.src == 10.43.54.65 or ip.dst == 10.43.54.65

## Package analysis

You can see the `hd` like byte content of the package. By clicking on the bytes, Wireshark shows what part of it's parsed output those bytes correspond to!
