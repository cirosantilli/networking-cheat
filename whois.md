# WHOIS

The WHOIS protocol, and utilities that implement it.

## WHOIS protocol

Given a hostname of IP, check info about it like country, ISP.

TCP/43.

<http://tools.ietf.org/html/rfc3912>

<http://en.wikipedia.org/wiki/Whois>

## whois utility

`WHOIS` CLI interface.

    whois 201.81.160.156

    whois `curl ifconfig.me`

Needs a `WHOIS` server to work. According to `man whois`, it tries to determine a server intelligently, and if that fails it resets to a default server: `whois.arin.net` for IPv4 addresses. It seems that the "intelligent" server discovery method is not standardized: <http://en.wikipedia.org/wiki/Whois#Server_discovery>.

TODO: understand it's output in detail. Why is hostname output `whois hotsname.com` different from IP output `whois 173.194.40.194`?
