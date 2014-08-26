# ARP

Address Resolution Protocol.

<http://tools.ietf.org/html/rfc826> 1982

Only used when the sender detects that the searched IP is on the same network as itself.

In that case, it can simply get the destination MAC address and send the packages over the LAN directly to the destination without passing through the router.

ARP is a protocol that does just that: it finds the MAC address from an IP on a LAN.

## ARP table

Cache that caches previously resolved IP to MAC addresses so that ARP requests don't need to be made every time.

Also known as: ARP cache, MAC cache.

## arp utility

CLI utility that shows the ARP table.

To see a computer on the table, remember that you first must have tried to contact it somehow,
so first ping that computer:

    timeout 3 ping 192.168.1.3; arp -a
