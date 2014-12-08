# tcpdump

CLI utility that allows to visualize TCP packets sent and received.

Good intro tutorial: <http://danielmiessler.com/study/tcpdump/>

Also consider Wireshark, which further parses the output to make it easier to interpret (but unfortunately is a X GUI instead of CLI).

## Filter

## pcap-filter

Filter syntax is documented at:

    man pcap-filter

The library is called `libpcap`.

Host:

    sudo tcpdump -i eth0 'host example.com'

Only show replies from given host, not requests you send:

    sudo tcpdump -i eth0 'src host example.com'

Port 80:

    sudo tcpdump -i eth0 'tcp port 80'

Logical operations: and:

    sudo tcpdump -i eth0 'host example.com &&  tcp port 80'
    sudo tcpdump -i eth0 'host example.com and tcp port 80'

## i

Select interface to listen:

    sudo tcpdump -i eth0

Most useful options:

-   `-X`: show ASCII and hex side by side:

-   `-n`: don't resolve hostnames, show numeric IPs

-   `-vvv`: maximum verbosity level

    Interprets standard data types and prints them, making output easier to understand.

Example:

    sudo tcpdump -SXn

## TLS

## HTTPS

Not possible to view the decrypted data as it may span across multiple TCP packets, and tcpdump does not reassemble them.
