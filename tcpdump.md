# tcpdump

CLI utility that allows to visualize TCP packets sent and received.

Good intro tutorial: <http://danielmiessler.com/study/tcpdump/>

Also consider Wireshark, which further parses the output to make it easier to interpret (but unfortunately is a X GUI instead of CLI).

Most useful options:

-   `-X`: show ASCII and hex side by side:

-   `-n`: don't resolve hostnames, show numeric IPs

-   `-S`: don't resolve hostnames, show numeric IPs

-   `-vvv`: maximum verbosity level

    Interprets standard data types and prints them, making output easier to understand.

Example:

    sudo tcpdump -SXn

