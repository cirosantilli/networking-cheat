# IP

Protocol that allows to:

- assign addresses to network interfaces.
- find path between one computer to another, possibly passing through many routers
- fragment and reassemble messages that are too large to be reliably transmitted through on a channel

Main spec: <http://tools.ietf.org/html/rfc791>, from 1981!

## Versions

## IPv6 vs IPv4

As of 2014, the dominant version is IPv4, but IPv6 is already specified and has many implementations.

One of the major limitations of IPv4 is the small number of addresses, which was underestimated at the creation of IPv4, making such addresses a valuable resource.

IPv4 addresses have 4 bytes, while IPv6 have 16, making the number of addresses for IPv6 stratospherically large at around 10^38. Compare it to Avogadro's constant at 10^23, so it is unlikely that those addresses will become a valuable resource in the foreseeable future.

Unless where noted otherwise, the term IP will refer in this project to either IPv4 or IPv4 and IPv6 unless explicitly written IPv6.

## IPv5

Already taken by a revision of some old protocol: <http://archive.oreilly.com/pub/post/what_ever_happened_to_ipv5.html>

## IP header

Learn what the IP header contains:

<http://en.wikipedia.org/wiki/IPv4_header#Header>

Fields by increasing interest / ease to understand ratio:

-   **Version (4 bits)**

    Indicates the protocol version.

    Value 4 for IPv4.

-   **Internet header length (IHL) (4 bits)**

    Length of the IP header only (no data) in 4 byte units.

-   **Total length (2 bytes)**

    Total length of header + body in bytes

    This must be transmitted as the length is variable.

-   **IPs of destination and origin (4 bytes each)**

-   **Time to live (TTL) (1 byte)**

    Decreased whenever the packet passes through a router.

    If 0, router does not forward the package, because it has already traveled for too long, and signals this to the sender via an ICMP Time exceeded with Code = 0.

    This prevents lost packages from doing infinite turns on the network.

-   **Header checksum (2 bytes)**

-   **Protocol (1 byte)**

    Number that identifies the protocol contained in the IP data, for example TCP or UDP.

    In this way, the receiver knows how to interpret the data inside of the IP package.

    The numbers are assigned by IANA and can be found [here](http://en.wikipedia.org/wiki/List_of_IP_protocol_numbers).

-   **Flags 3 bits**

    Used for IP fragmentation.

-   **Fragment offset (13 bits)**

    Used for IP fragmentation.

-   **Identification (2 bytes)**

    Used for IP fragmentation.

## IP address

An unique address that identifies a host, for example a separate workstation.

IPv4 addresses are 4 bytes long.

Each computer knows its IP address.

This can be set in two ways:

-   statically and manually: admin enters those values for each computer.

    They never change.

-   dynamically and automatically specially via DHCP.

    Best method on a local network to avoid IP clashes.

### Address representation

### Notation

The most common notation by far is the dotted decimal:

    192.156.0.1

With the advent of CIDR, a new notation was introduced that also specifies the subnet mask in one go:

    192.156.0.1/24

### Classes

Each address has two parts: network part and host part.

How many bytes are the network, and how many bytes are the host was determined only by the class of the IP before CIDR, and only the following possibilities existed:

- `A`: starts with 0
- `B`: starts with 10
- `C`: starts with 110
- `D`: starts with 1110
- `E`: all others

IPs on classes `A`, `B` and `C` were reserved for LAN usage;.

If your address is in those ranges, the routers proxy server knows it is an internal one you are asking about.

The most common home range, specially in home networks, is the Class C:

    192.168.0.1 through 192.168.255.254
    subnet mask 255.255.255.0

CIDR added the possibility to use any network / host split.

#### Example: network and host parts

- 2 networks
- 3 bytes for the network part
- 1 router
- 3 computers on the same network

Things could look like:

    +---------------+   +---------------+   +---------------+
    | Computer 1    |   | Computer 2    |   | Computer 3    |
    |---------------|   |---------------|   |---------------|
    | 192.156.0.2   |   | 192.156.0.3   |   | 192.156.0.4   |
    +---------------+   +---------------+   +---------------+
    |                   |                   |
    |                   |                   |
    +-------------------+-------------------+
    |
    |
    +---------------+
    | 192.156.0.1   |
    |---------------+
    | Router        |
    |---------------|
    | 192.157.0.1   |
    +---------------+
    |
    |
    +-------------------+-------------------+
    |                   |                   |
    |                   |                   |
    +---------------+   +---------------+   +---------------+
    | Computer 3    |   | Computer 4    |   | Computer 5    |
    |---------------|   |---------------|   |---------------|
    | 192.157.0.2   |   | 192.157.0.3   |   | 192.157.0.4   |
    +---------------+   +---------------+   +---------------+

Note how the router also has one ore more local IPs.

In this example, there are 2 local networks: for one to communicate with the other, they must first pass through the router.

When the router sees an address that is not in the current network, e.g. Computer 3 wants to send to Computer 1, it is able to determine to which network to send it. TODO how.

### LAN IP

### Internal IP

### WAN IP

### External IP

If you use a router, your entire network has a single IP seen from the outside (WAN), and an internal IP for each interface seen on the private local network (LAN).

You external IP may change or not depending on how your ISP operates. Most ISPs to modify home user's IPs from time to time to have more flexibility, but many give you the same external IP for at least several hours, making it possible for you to use it for simple development.

This only matters if you want to have external computers make requests for you, e.g. to serve a web server to the outside world. Replies to requests you make already know where to be routed back to from information sent on the request.

If you are going to use your external IP behind a router, you will need to enable port forwarding.

Get LAN IPs for all interfaces on current computer:

    ifconfig | grep -B1 "inet addr" | awk '{ if ( $1 == "inet" ) { print $2 } else if ( $2 == "Link" ) { printf "%s:" ,$1 } }' | awk -F: '{ print $1 ": " $3 }'

Each interface has its own IP. Therefore, a single computer can have multiple LAN IPs: one for the wired connection, one for the Wiki, one loopback localhost, etc.

The server on the router is called **proxy server**.

Internal IPs may be assigned automatically via the DHPC protocol.

#### Get external IP

    curl ipecho.net/plain
    curl ifconfig.me

- <http://askubuntu.com/questions/95910/command-for-determining-my-public-ip>
- <http://unix.stackexchange.com/questions/22615/how-can-i-get-my-external-ip-address-in-bash>

### Port forwarding

By default, if a TCP / UDP SYN request is made to most routers, they are simply dropped.

This means that you cannot serve a web server from behind the router.

To avoid this, you must set up *port forwarding*, that specifies certain ports and protocol (TCP or UDP) that will be sent to a local LAN IP.

It seems only possible to do this if the local IP is static TODO confirm.

### Classless routing

### Subnet mask

Classless Inter-Domain Routing: the number of network bits is part of the address.

<http://tools.ietf.org/html/rfc1517>, 1993.

### Prefix length

Name of the analogous concept in IPv6, but restricted to the far dominating case of 1's prefixed mask.

In theory, IPv4 subnet masks could be any 4 byte pattern as the name suggests.

#### Get mask for an interface:

    ifconfig wlan0 | sed -nr 's/.*Mask:([^ ]*)/\1/p'

Internal IPs have two parts: network and computer.

The length of the network part may vary between networks.

The length is given by the **subnet mask**, e.g.:

    255.255.255.0
    1111.1111.1111.0000

means that 12 first bits are network.

    255.255.0.0

means that 8 first bits are network

All computers in the same network must have the same subnet mask and the same network part, but different computer parts.

Each network (formally **network segment**) is run by a single router #TODO confirm

#### CIDR notation

It is cumbersome for humans to write the subnet mask with 4 integers, so a simpler notation as introduced. E.g.:

    255.255.255.0/24

means:

- address: `255.255.255.0`
- subnet mask: 24 leading ones

### Special purpose addresses

Specified on <http://tools.ietf.org/html/rfc6890#section-2.2.2> 2013, which determines current best practices, links to many other RFCs that define things.

Some special addresses were specified in separate RFCs than the main IP specs.

#### Zero host address

<http://serverfault.com/questions/135267/what-is-the-network-address-x-x-x-0-used-for>

If the entire host part is zero, then the address is used to refer to the network itself.

It is used when several networks, one one a different router must speak to each other.

#### 0.0.0.0

#### Default gateway

`0.0.0.0` host address in the routing table.

If no network matches request, sends to this network.

Address you get automatically redirected to by router if the address you gave cannot be found on the local network.

Find default gateway:

    route -n

or for programmatic usage:

    route -n | awk '{ if( $1 ~ "0.0.0.0" ) print $2 }'

If a program is listening to `0.0.0.0`, then it is listening on all interfaces:

- localhost `127.0.0.1`
- local IP address of WIFI or Ethernet, e.g. `192.168.0.2` (retrieved with `ifconfig`)

So if you do:

    nc -l 0.0.0.0 8000

it will catch all of:

    echo a | nc -l 0.0.0.0     8000
    echo a | nc -l 127.0.0.1   8000
    echo a | nc -l 192.168.0.2 8000

On `ifconfig`, this value appears on the `Foreign address` column if there are not programs listening to the connection.

The same goes for writing:

    nc -l 127.0.0.1 8000 &
    echo a | nc -l 0.0.0.0     8000

#### Unspecified IPv6 address

<http://superuser.com/a/802793/128124>

In IPv6, `::` means `0:0:0:0:0:0:0:0`, and is the *unspecified address*.

For example, it appears on the `ifconfig` `Foreign address` column when there are no programs listening to a server.

#### 127.0.0.1

#### Loopback

<http://tools.ietf.org/html/rfc1122#section-3.2.1.3> 1989

Always refers to the current address.

#### localhost

Common hostname associated to the loopback address.

#### localhost.com

Redirects to `google.com`. Try it: [localhost.com](localhost.com).

<http://stackoverflow.com/questions/21768626/localhost-com-redirects-to-google-search>

One theory is that a current Google employee owns it. He bought it when the web was still young and kept it ever since.

### Broadcast address

#### 255.255.255.255

Specified in: <http://tools.ietf.org/html/rfc919> 1984.

The host address is entirely composed of 1s.

The broadcast address means talking to all computers on a given network at once instead of a single computer.

TODO: simple example with an utility that does something magic with it?

### 169.254.0.0/16

### Link-local address

<http://en.wikipedia.org/wiki/Link-local_address>

TODO what is it? My interpretation: addresses that belong the current network segment and never get forwarded by the router.

Used on zeroconf.

### Examples

#### Class C network

-   network part: `192.168.3`

    Broadcast is: `192.168.3.255`

-   network part: `192.168.234`

    Broadcast is: `192.168.3.255`

#### Class A network

-   network part: `10`

    Broadcast is: `10.255.255.255`

#### .1 addresses

The `.1` address is not special, but in home networks is often already taken by the router's inner interface

This is why your addresses may start at `.2`.

## Fragmentation

If a router will forward this packet to another interface, it may be that the MTU of the other interface be smaller than the previous one, and the package cannot be transmitted hole.

In this case, IP provides a fragmentation mechanism to split up the package into smaller ones.

TCP always attempts to avoid IP fragmentation, so that if TCP is being used, it is unlikely that fragmentation will happen. It is therefore easier to observe IP fragmentation for UDP packets.

Reassembly of fragments is meant to happen only at the final destination.

The fields used from the IP header  are:

-   flags (3 bits)

        | 0 | DF | MF |

    -   0: reserved, always set to 0

    -   `DF`: don't fragment.

        If that is the case, the router first checks the `DF` flag.

        If `DF = 1`, routers should not fragment, and notify the source via an ICMP fragmentation not possible message.

        If `DF = 0`, fragmentation can occur, and tranmission continues.

    -   `MF`: more fragments

        If 0, this is the last fragment.

        If 1, there are more fragments to come.

        A common technique is to send first the last segment, which allows the destination to know how large a buffer it will need in advance.

-   fragment offset: (13 bits)

    How many 8 byte units of IP data have already been sent.

    The first fragment always has this field equal to 0.

    But if fragmentation occurs, the following fragments will contain how much IP data has been sent already.

-   identification: (2 bytes)

    It is set by the sender with a different number for each IP datagram.

    If fragmentation will occur, each fragment's IP header will get the same identification number so that they can be reassembled afterwards.

    Identification numbers can be the same across multiple source IPs,
    but for a single source IPs they are unique.

## NIC

Network Interface Cards

Hardware that does network communication.

Come mostly built-in the motherboard today.

Each router has at least 2 NICs: one external and one internal.

### get all interface names

    ifconfig | perl -ne '/^(\S+)/ && print $1 . "\n"'

## MAC

AKA:

- physical address
- hardware address
- media access control address
- BIA: burnt in address

Unchangeable address of each NIC.

Unique across and within vendors.

6 bytes: first 3 identify vendor, last 3 product

Colon separated notation. Ex: `0C:21:B8:47:5F:96`.

Get MAC addresses of my computer:

    ifconfig

Or for programmatic usage:

    ifconfig | sed -nr 's/([^ ]*) .*HWaddr (.*)/\1 \2/p'

Get MAC addresses of computers I have already talked to in the LAN:

    timeout 3 ping 192.168.1.3
    arp -a | sed -nr 's/([^ ]*) .*at (.*)/\1 \2/p'

## ICMP

Internet control message protocol.

Part of the IP protocol.

Contains several types of information for routers about the IP traffic.

Lives in the Internet layer, but is encapsulated inside an IP datagram just like TCP and UDP:

    | ICMP data |
    | IP data   | IP header |

Its number on the IP protocol field is 1, so you may guess that this is a very important protocol.

Structure of the ICMP header

- Type 1B
- Code 1B
- Checksum 2B
- Variable part 4B

ICMP can also contain an optional extra data section after the variable part.

Examples of what ICMP can do:

-   ECHO requests.

    The server responds with another echo request with the same data.

    Used to testing the network.

    Used by the ping utility.

-   destination unreachable

    Datagram cannot be transmitted further by a router.

    TODO when does this happens? This is not the `TTL = 0` since that is covered by Time Exceeded

-   source quench

    Router tells source to lower sending speed because the network is too overloaded.

-   redirect

    Router tells source to not make that request again, since there is an obvious better choice of router to make the request to.

    Typically happens on a LAN with 2 routers, if a host sends a request to a bad router.

-   router discovery

    Source asks for any routers on the LAN to identify themselves.

-   time exceeded.

    -   if `code = 0`: TTL reached 0.

        Router notifies source of that via ICMP.

        Used by `traceroute`.

    -   if `code = 1`: reassembly time exceeded.

        IP headers can be fragmented.

        If the first part reaches, but the second takes too long, the reciever discards the first to make room for other requests, and notifies the source like this.

-   packet too big (BTP)

    Sent by router to source if it receives a packet that is larger than the MTU and the IP header has `DF = 1`.

## IGMP

Internet Group Message Protocol.

Similar to ICMP in some senses:

- wrapped inside IP
- IP control purposes

However IGMP is used to control multicasts only.

IP protocol number: 2.

## IPv6

<http://tools.ietf.org/html/rfc4291> 2006.

### Try it out

Terminal 1:

    nc -l6 ::1 8000

Terminal 2:

    echo a | nc -6 ::1 8000

Congratulations, your first explicit IPv6 connection!

### Usage

IPv6 is less popular than IPv4 as of 2014, but it's popularity is increasing, and it is likely that it will dominate in a few years.

A few metrics:

- percentage of Google requests on IPv6: <https://www.google.com/intl/en/ipv6/statistics.html>

### IPv6 address representation

### IPv4 notation

Rules documented at: <http://tools.ietf.org/html/rfc5952>

Full addresses are:

- written in hex. The characters `a-f` must be lowercase.
- grouped every 2 bytes by `:`
- leading 0's *MUST* be removed. `0000` becomes `0`.
- the longest sequence of `:0:0:0:` *MUST* be converted to `::`. Can only be done once, or would lead to ambiguity.
- a single `:0:` shall not be converted to `::`, only multiple 0's like `:0:0:`.

The rules that make addresses shorter lead to the *cannonical* representation of the address: they are not just optional.

Good:

    2001:db8::ff00:42:8329

which means in a fuller notation:

    2001:0db8:0000:0000:0000:ff00:42:8329

Bad, uppercase:

    2001:DB8:0:0:0:FF00:42:8329

Bad, leading 0's:

    2001:0db8::ff00:42:8329

Bad, 0's not compressed:

    2001:db8:0:0:0:ff00:42:8329

## traceroute

CLI utility that shows each step an IP package takes to reach a destination.

The program sends the request with TTL = 1, TTL = 2, TTL = 3, and so on, and gets the address at which it stopped via ICMP time exceeded router responses.

Example:

    traceroute www.google.com

## Router

Routers send packages from one place to another.

Most routers will have at least two network interfaces, one on each network.

### Configure you router

If you want to play around with routers, you should get your hands dirty and do some router configuring.

Routers are generally configured through a browser.

First you must make a wired connection to the router.

You must enter the IP address of your router. This is fixed and supplied by the router manufacturer. A common address is the first address of the range: `192.168.0.1` for class C.

You must then enter a username and a password. A default will be supplied by the manufacturer, such as `admin` `admin`, or `admin` `password`. This can be changed once you logged in.

### Routing table

Great LAN routing example: <http://en.wikipedia.org/wiki/Default_gateway>

Routing tables say: if the request should go to a given network, send it to a given interface.

`0.0.0.0` is the default if no other is found.

Routers have two interfaces each: inside and outside.

## Kernel routing table

TODO: what is it?

## route utility

View kernel routing table:

    route

Numeric instead of names:

    route -n

## Famous IPs

-   8.8.8.8 and 8.8.8.4: <http://en.wikipedia.org/wiki/Google_Public_DNS>
