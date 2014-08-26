# tcpflow

Show data send and read at a given port.

Parses output intelligently somewhat like Wireshark to make it easier to read.

<http://superuser.com/questions/23180/whats-the-easiest-way-to-sniff-tcp-traffic-data-on-linux>

Ubuntu 12.04 install:

    sudo aptitude install tcpflow

Sample usage:

    sudo tcpflow -i any -C -e port 1234

