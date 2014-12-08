# Wireshark

Set of utilities that that capture (sniff) TCP packages similarly to `tcpdump`.

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

## Keyboard shortcuts

- `Ctrl + E`: start / stop capture

## Filters

In order to get any meaningful data, it is very important to filter the packages.

There are two kinds of filters:

- capture
- display

### Capture filters

Determine what will be passed on to Wireshark.

Configured under Capture > Options `Ctrl + K`.

The syntax is the same as:

    man pcap-filter

The library is called `libpcap`, which is also used by `tcpdump`.

Will not be further discussed here.

The main advantage over display filters is that they can reduce the amount of data captured.

### Display filters

<http://wiki.wireshark.org/DisplayFilters>

<https://www.wireshark.org/docs/dfref/>

Filters what Wireshark will show on the UI.

Much more powerful than capture filters, since at this point Wireshark has already parsed the packets.

You can create new custom filters from Analyse > Display filters.

Some sample filter expressions:

    tcp.port == 80 || udp.port == 80

Where `tcp.port` and `udp.port` are the ports on either endpoint.

IP of any endpoint:

    ip.addr == 10.43.54.65

Same as:

    ip.src == 10.43.54.65 or ip.dst == 10.43.54.65

By hostname:

    ip.dst_host == example.com

Any endpoint:

    ip.addr_host == example.com

Name resolution must be enabled for that to work.

## Options

Preferences under Ctrl + Shift + P

### Name resolution

If enabled:

- you will be able to use hostnames instead of IPs in places like display filters
- resolved hostnames will show on the capture output

Preferences > Name Resolution and tick:

- Resolve network (IP) addresses
- Use an external network name resolver

## Package analysis

You can see the `hd` like byte content of the package. By clicking on the bytes, Wireshark shows what part of it's parsed output those bytes correspond to!

## Alternatives

- TShark: command line Wireshark. Also from the Wireshark project.

## HTTPS

It is complicated to understand HTTPS transactions with Wireshark. TODO you may need the server's private key.
