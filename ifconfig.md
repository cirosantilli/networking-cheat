# ifconfig

`Network InterFace configuration tool`.

Get and set key interface information like IP, subnet masks, MAC, etc.

Good source: <http://www.thegeekstuff.com/2009/03/ifconfig-7-examples-to-configure-network-interface/>

    ifconfig

Sample interfaces on a modern laptop:

- `eth0`:  wired network 0
- `wlan0`: Wifi card 0
- `lo`:    loopback (local host)

Get local IPs (behind router):

    ifconfig | grep -B1 "inet addr" | awk '{ if ( $1 == "inet" ) { print $2 } else if ( $2 == "Link" ) { printf "%s:" ,$1 } }' | awk -F: '{ print $1 ": " $3 }'

`wlan0` and `eth0` are two different interfaces!

## iwconfig

Wireless network configuration

TODO: subset of `ifconfig`?

## See also

NetworkManager is a higher level network configuration tool used in Ubuntu 12.04 by default. You should use that instead if installed.
