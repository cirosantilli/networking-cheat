# netstat

CLI utility that shows lots of socket info.

Very important networking debug tool.

Lists PID and program name of programs using ports.

Shows both TCP/UDP Internet connections and UNIX domain sockets.

In short: Internet connections are done via sockets whose address is given by an IP and a port number, and can communicate across computers.

UNIX domain sockets are only for local communication. They are put into the filesystem and identified by a path on the filesystem.

When a program uses a socket, it binds to it, and other programs cannot use it.

Most useful options:

- `n`: don't resolve IPs into hostnames. Greatly speeds up the output generation.
- `a`: show both listening and not listening ports.
- `p`: show program name and PID.
- `t`: show only TCP
- `u`: show only UDP
- `x`: show only UNIX sockets
- `i`: show information on interfaces.
- `r`: show kernel routing table.
- `s`: show statistics on several protocols.

Sample output for Internet section:

    Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
    tcp        0      0 localhost:32842         localhost:48553         ESTABLISHED 3497/GoogleTalkPlug

Meaning of the fields:

- `Proto`:            protocol: TCP or UDP
- `Recv-Q`:           TODO
- `Send-Q`:           TODO
- `Local Address`:    local IP and port of the connection. TODO can this only be either `localhost` or local network (192.168.X.X on type)?
- `Foreign Address`:  the IP and port of the remote endpoint. `0.0.0.0:*` appears when `LISTEN` on IPv4 and `:::*` on IPv6.
- `State`:            one of the standard TCP states for the connection. Empty for UDP since it is stateless.
- `PID/Program name`: self explanatory

It does not seem possible to see the full arguments passed to each process as `ps -A` does. This is a problem, since for interpreted languages, several processes may look the same without arguments, e.g. `node` and `node` instead of `node script1` and `node script1`. The best workaround so far is to `awk` and pipe into  `ps`: <http://serverfault.com/questions/576910/how-to-find-full-process-arguments-and-associated-listening-ports>

## Sample output lines

Let's take a look at some typical output lines to better understand the fields.

Running:

    python -m SimpleHTTPServer

gives the line:

    Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
    tcp        0      0 0.0.0.0:8000            0.0.0.0:*               LISTEN      21267/python

since:

- `Local Address` is `0.0.0.0:8000`, this means that the server is listening to all interfaces on port 8000. TODO: why?
- `Foreign Address` is `0.0.0.0:*`, this means that no one is currently listening. This will almost always be the case for web servers like this, since HTTP is stateless that closes the connection quickly, so you shouldn't be able to notice it. TODO: why? <http://unix.stackexchange.com/questions/107962/what-does-0-0-0-0-mean-in-netstat>

If it were on IPv6 (`Proto` == `tcp6` or `udp6`), it would be `:::*` instead of `0.0.0.0:*`, which means the same in this context.
