# ping

CLI utility that sends ICMP echo requests to a server that accepts them.

It measures the time it takes for the answer to come back, which is a measure of connectivity between the two computers.

The term is widely known to online gamers, where the term *the ping*, which few gamers know is the send and return time, is a measure for connection quality.

The default IANA port for ping requests is port 7/TCP or 7/UDP.

Send an echo every second to monitor connectivity:

    ping www.google.com
