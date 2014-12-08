# TCP and UDP

## TCP vs UDP

Different protocols, but with some common functions.

Some application layer protocols include both a TCP and a UDP version, which may vary slightly, while others only have either a TCP or an UDP version. For lit of registered protocols see: <http://en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers>

### Acknowledgement receipt and resending

### Duplicate removal

TCP guarantees that information packages arrive by requiring the receiver to send acknowledgement receipts back, and if the receipt is not received, it will send the package again for a certain number of times until it is. This means that a package can be sent and received multiple times, and TCP also takes care of eliminating duplicates.

UDP has no such mechanisms.

For this reason alone:

- TCP must be used when it is important that precise sequences of data are sent and received.
- UDP is only suitable for sending fixed small amounts of data that can be sent in a single package, when it is not crucial that we be sure each one arrived. Protocols that use it may send the data many times until a response is obtained, and usually only expect at most one reply.

### Flow control

TCP can control how fast data is sent between client and server, to ensure that one side does not send data faster than the other can process.

### Congestion control

TCP implements a few standard algorithms to prevent congestions, that is, to globally optimize network performance.

The main algorithms are described at: <http://tools.ietf.org/html/rfc5681>. They are:

- slow-start
- congestion avoidance
- fast retransmit
- fast recovery

What congestion control means exactly depends on the network model and what is trying to be optimized. The TCP algorithms aim to approximate a min-max fair allocation: <http://en.wikipedia.org/wiki/Max-min_fairness>. TODO understand exactly what is the model, and what is min-max fair.

### Duplex or not

In order to acknowledge that packages were received in both directions, data must be able to be sent in both directions. This means that the TCP protocol is duplex.

UDP is not: data can only be sent in one direction. Of course, sender and receiver can both communicate in this manner in both directions, but the way they so is not specified in UDP, while it is in TCP, and in a very efficient and standard manner, so there is no interest in doing so with UDP.

### Stateless or not

In order to have acknowledgement receipts TCP, has to maintain a connection state, while UDP simply sends the packages and hopes for the best.

This means that UDP is stateless, while TCP is not: TCP client and server must keep information in their memory that there is a connection going on, even when nothing is being sent.

For this reason, UDP has less time and memory overhead, but is only used when the transaction will limit itself to a single request/answer. UDP can handle many more clients at once.

Many application layer protocols however, e.g. HTTP, are stateless: they open a TCP connection, and close it as soon as they get a reply back.

## Fragmentation

TCP and UDP do not deal with fragmentation: each message send is received as a single message, even though it may have been fragmented over the network by IP.

## TCP

Transmission Control Protocol

<http://tools.ietf.org/html/rfc793>

### TCP Header

The actual data that TCP contains:

-   Source port (2 Bytes)

-   Destination port (2 Bytes)

-   SEQ number (4 bytes)

-   ACK number (4 bytes). Only meaningful if ACK flag set.

-   Data offset (4 bits). The total size of the header in multiples of 4 bytes. The minimum size is 5 x 32, in which there is no optional data. Anything larger than 5 goes into the data.

-   Reserved (3 bits). Reserved for future usage. Should be set to 0 now.

-   9 control bits (9 bits):

    The most important ones:

    - ACK: the ACK field is significant. Set for all packets except the initial SYN.

    - SYN: this is a synchronizer packet. Only sent for the first packet of client and server.

    - FIN: sender wants to close the connection.

-   Window size (2 bytes): number of bits that sender can receive.

-   Checksum (2 bytes): checksum of header data for error detection.

-   Urgent pointer: TODO.

-   Options:  Variable size 0–10 bytes, multiple of 4 bytes 0 padded, exact size determined by  the Offset field.

Learn the TCP header:

<http://en.wikipedia.org/wiki/Transmission_Control_Protocol#TCP_segment_structure>

### Open connection

### 3-way handshake

### Three-way handshake

In order to acknowledge connections, each sent package has a number, and the receiver must send back a receipt with the received number.

Therefore, so setup a duplex connection, that needs to be done in both directions: 

- sender sends a random package ID number, receiver sends back ACK
- the other way around

The 3-way handshake sets that process up. It uses 3 packages, because that is the minimum needed to set up both sides of the connection:

- package 1: SYN: sender sends his package ID to synchronize
- package 2: ACK+SYN: receiver sends his package ID (SYN) and acknowledges the receiver's (ACK)
- package 3: SYN: sender acknowledges the receiver's ID

So 1-2 sets up the data sending channel, and 2-3 sets up the channel back.

TODO: when does actual data start being sent? In package 3 already, or later? Does it depend on the application protocol being used?

### Close connection

The same process is used when closing the connection, except that the FIN bit is used instead of the ACK bit.

Either side can initiate the connection closure.

The connection can half open if only one of the sides sends FIN. In that case, the side that closed cannot send any more data.

### States

There are a few predefined states which server and client can be.

- `LISTEN` (server) represents waiting for a connection request from any remote TCP and port.

- `SYN-SENT` (client) represents waiting for a matching connection request after having sent a connection request.

- `SYN-RECEIVED` (server) represents waiting for a confirming connection request acknowledgment after having both received and sent a connection request.

- `ESTABLISHED` (both server and client) represents an open connection, data received can be delivered to the user. The normal state for the data transfer phase of the connection.

- `FIN-WAIT-1` (both server and client) represents waiting for a connection termination request from the remote TCP, or an acknowledgment of the connection termination request previously sent.

- `FIN-WAIT-2` (both server and client) represents waiting for a connection termination request from the remote TCP.

- `CLOSE-WAIT` (both server and client) represents waiting for a connection termination request from the local user.

- `CLOSING` (both server and client) represents waiting for a connection termination request acknowledgment from the remote TCP.

- `LAST-ACK` (both server and client) represents waiting for an acknowledgment of the connection termination request previously sent to the remote TCP (which includes an acknowledgment of its connection termination request).

- `TIME-WAIT` (either server or client) represents waiting for enough time to pass to be sure the remote TCP received the acknowledgment of its connection termination request. [According to RFC 793 a connection can stay in TIME-WAIT for a maximum of four minutes known as a MSL (maximum segment lifetime).]

- `CLOSED` (both server and client) represents no connection state at all.

TODO: understand precisely the `FIN` and `CLOSING` states.

Only certain state transitions are possible in normal operation depending on the inputs, so it can be modeled with as a finite state machine: a simplified diagram can be found at: <http://en.wikipedia.org/wiki/File:Tcp_state_diagram_fixed_new.svg>

## Implementations

TCP is so important and time critical that it is implemented directly in the Kernel, and not on user space applications.

### Manually send bytes

TODO Do you need a kernel module to tamper the exact TCP bytes?
