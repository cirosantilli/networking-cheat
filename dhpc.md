# DHCP

Dynamic Host Configuration Protocol.

- IPv4: <http://tools.ietf.org/html/rfc2131> (1997)
- IPv6: <http://tools.ietf.org/html/rfc3315> (2003), extended by many others

<http://en.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol>

Application layer protocol that automatically assigns configurations to the hosts on a network, such as their IP.

Default IANA ports: UDP 67 and 68 (same as its less advanced and less common predecessor `BOOTP`).

When a computer enters a network and it does not know its own IP.

It must first send a DHCP request to be assigned an IP.

The server usually (TODO always?) runs in the router, and can be configured from the router's interface.

## Static IP

On a home network that you control, it is better to use intuitive hostnames and let the addresses be dynamically set via DHPC, unless you absolutely need a static IP, for example to setup a server behind your router.

DHPC does not know about static IPs: if you set one you must make sure that it is outside of the DHPC range. DHPC is done by the router, and should be configurable from the browser interface.

On my Numericable router, under Network > Basic Settings > IP LAN > I have two fields: `Starting IP Address` and `Ending IP Address` which allow me to control it. By default, the range is 192.168.0.10 to 192.168.0.50, which is a sensible default allowing for 8 small static IPs between 2 and 9, 1 being the router's address.

On Ubuntu 12.04, there are a few ways of doing it.

## Static IP with DHCP reservation

This is not strictly static, but it is the simplest option.

On you router configuration, find the DHCP Reservation Lease Infos.

This allows you to map LAN IPs directly to MAC addresses.

On my Numericable router it is found under the IP LAN tab.

Set the interface to use DHCP.

The assigned address must be in the DHCP range.

## Static IP with NetworkManager

Using the NetworkManager GUI:

    nm-connection-editor

Select connection > Edit > IPv4 settings, configure.

TODO fails. I lose internet connection on the interface

## Static IP with NetworkManager

Using `/etc/network/interfaces`:

    sudo vim /etc/network/interfaces

Set the file to:

    auto lo
    iface lo inet loopback

    auto eth0
    iface eth0 inet static
    address 192.168.0.2
    netmask 255.255.255.0
    gateway 192.168.0.1
    dns-nameservers 89.2.0.1
    dns-search example.com
    #network 192.168.0.0

- `auto if1 if2`: automatically create interfaces `if1` and `if2` on `ifup -a`.
- `iface if1`: from now on, define properties of `if1`.

TODO fails. I lose internet connection on the interface

## See also

`zeroconf`
