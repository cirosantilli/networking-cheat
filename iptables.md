# iptables

Configure what the Kernel should do to networking communication.

It is a firewall.

Implemented as a kernel module:

    lsmod | grep ip_tables

Outputs:

    ip_tables  27239  2  iptable_filter,iptable_nat
    x_tables   34059  4  ip_tables,xt_tcpudp,iptable_filter,xt_REDIRECT

## L

List.

View state:

    sudo iptables -L

Sample output before we do anything:

    Chain INPUT (policy ACCEPT)
    target     prot opt source               destination

    Chain FORWARD (policy ACCEPT)
    target     prot opt source               destination

    Chain OUTPUT (policy ACCEPT)
    target     prot opt source               destination

The only information we have is that `INPUT`, `FORWARD` and `OUTPUT` are accepting connections.

Even more information can be obtained with `-v`.

## F

Clear all current rules:

    sudo iptables -F

Great after we're done with messing things up.

Does not affect policies set by `-P`.

## t

`iptables` can deal with multiple types of table.

Each table has their possible policies and targets.

## iptables-save

Save current configuration. Otherwise, it gets destroyed when `iptables` restarts.

## Policies

### INPUT

Incoming requests.

    sudo iptables -P INPUT DROP

Now:

    sudo iptables -L

says:

    Chain INPUT (policy DROP)

and:

    curl --connect-timeout 3 127.0.0.1

times out, because the request was sent, but the response got dropped.

Restore:

    sudo iptables -P INPUT ACCEPT

### FORWARD

Packets that the system would forward to another system.

Happens if your system is acting as a router.

### OUTPUT

Outgoing requests.

Drop any packages before they are output:

    sudo iptables -P OUTPUT DROP

You lose all Internet connection.

    curl example.com

gives:

    curl: (6) Could not resolve host: example.com

since it cannot find the DNS, and:

    curl --connect-timeout 3 127.0.0.1

times out:

    curl: (28) Connection timed out after 3000 millisecond

as it tried to send the request, but no response came because it was not actually sent.

Restore it:

    sudo iptables -P OUTPUT ACCEPT

## Target

Targets are the actions taken on certain conditions.

Most targets are considered extensions and documented under:

    man iptables-extensiosn

but they come by default.

Targets are attempted sequentially: the order in which you add `-A` or insert `-I` them matters.

For example:

    sudo iptables -A OUTPUT -p tcp --dport 53 -j ACCEPT
    sudo iptables -A OUTPUT -p tcp --dport 80 -j ACCEPT
    sudo iptables -P OUTPUT DROP

for which `sudo iptables -L` reads:

    Chain OUTPUT (policy DROP)
    target     prot opt source               destination
    ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:domain
    ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:http

Allow output to port `80`, but deny any other:

    curl example.com
    curl github.com:443

TODO not allowing any traffic? Why?

### P

Default policy if no rule is matched.

## A

Append a new rule to the end of the list for a given policy:

    sudo iptables -A INPUT -s example.com -j DROP

## D

Delete a given rule.

    sudo iptables -D INPUT -s example.com -j DROP

Everything must match, except that `-A` is replaced by `-D`.

## I

Prepend a new rule.

### ACCEPT

Obvious.

### DROP

Don't simply don't send or receive.

### RETURN

TODO.

### REJECT

Extension.

Send some notification of the error, unlike `DROP` which is silent.

### REDIRECT

Only for `-t nat`.

Redirect all requests with given destination port `8000` to port `80`:

    sudo iptables -t nat -A PREROUTING -p tcp --dport 8000 -j REDIRECT --to-port 80

TODO not doing anything

## s

Specify source host to act on.

    sudo iptables -A INPUT -s example.com -j DROP

Now:

    curl --connect-timeout 3 example.com

times out, but:

    curl google.com

works.

## d

Destination to act on:

    sudo iptables -A OUTPUT -d example.com -j DROP

Now:

    curl --connect-timeout 3 example.com

times out, but:

    curl google.com

works.

## p

Specify protocol to act on: `tcp` or `udp`:

    sudo iptables -A OUTPUT -p tcp -j DROP

## dport

Specify port to act on:

    sudo iptables -A OUTPUT -p tcp --dport 80 -j DROP

Requires `-p`.

## Sources

- <http://wiki.centos.org/HowTos/Network/IPTables>

- <http://www.howtogeek.com/177621/the-beginners-guide-to-iptables-the-linux-firewall/>
