# nmap

Port scanner: show open ports, state and service name associated to the port.

Great way to start deciding how to go about hacking someone.

Ubuntu 12.04 install:

    sudo aptitude install nmap

TCP services:

    nmap google.com
    nmap localhost

You are gonna get at least 80 on Google for their HTTP server and on localhost too if you are running an HTTP server such as Apache.

Test on localhost:

    sudo nmap localhost

Sample output excerpt:

    PORT     STATE SERVICE
    22/tcp   open  ssh
    53/tcp   open  domain
    80/tcp   open  http

- `STATE`: TODO: maps to one of the standard TCP connection states, e.g. `open` == `Listen`?
- `SERVICE`: TODO: how does it determine the service? Just by using the default IANA registered one?

A `nc -l localhost 8001` shows as:

    8001/tcp open  unknown

View UDP ports:

    sudo nmap -sU localhost

Trivia: appeared in many hacking scenes of movies: <http://nmap.org/movies/#gijoe>
