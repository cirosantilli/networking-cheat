# Proxy Server

A server that takes requests, forwards them somewhere, waits for the response, and forwards the response back.

It can of course do whatever it wants to the response.

A proxy is both a server and a client.

## Forward proxy

AKA just Proxy.

A proxy server that sits between the clients and the Internet:

    client <---> proxy <---> firewall <---> internet <---> server

Functions:

- limit what clients can do, e.g., blacklist domains.

## Reverse proxy

The inverse of a forward proxy: one that sits between the internet and the server:

    client <---> firewall <---> internet <---> reverse proxy <---> server

Commonly used implementations are Apache and Nginx.

The typical application is when you have a script server that generates dynamic web pages like Rails or Django.

Rails and Django are ineffective at serving static files, while Apache and Nginx are super optimized for that. TODO how?

So you set up Apache or Nginx to serve every static file request under `/assets` from a directory, and pass the others to the server.

Another thing that those RPs do is load balancing, i.e. sending requests to the server that is the least loaded. TODO how?
