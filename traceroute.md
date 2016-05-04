# traceroute

CLI utility that shows each step an IP package takes to reach a destination.

The program sends the request with TTL = 1, TTL = 2, TTL = 3, and so on, and gets the address at which it stopped via ICMP time exceeded router responses.

Example:

    traceroute www.google.com
