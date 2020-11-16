Unlike the network client, all variables in the server are hardwired.     

Python is a language that execute **synchronously**.    

# What is socket

A network socket is an endpoint of an interprocess communication across a computer network.     

Sockets is a program that enables two sockets to send and receive data, bi-directionally, at any given moment.    

Python provides a *socket class* so developers can easily implement socket objects in their source code.    

# Create a socket

```python
import socket
# When specifying the socket type as socket.SOCK_STREAM, the default protocol that's used is the TCP
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
```

The **socket** function returns a socket object which has the following main methods:

+ sever sockets
      + bind()
      + listen()
      + accept()
+ client sockets
    + connect()

+ common for server and client
  + send()

   + recv()

The arguments passed to **socket()** specify the address family and socket type. **AF_INET** is the Internet address family for IPv4. **SOCK_STREAM** is the socket type for TCP, the protocol that will be used to transport messages in the network.      

The IP address **127.0.0.1** is the standard IPv4 address for the **loopback** interface, so only processes on the host will be able to connect to the server.



# The sequence of socket API calls and data flow for TCP:



![The sequence of socket API calls and data flow for TCP](https://files.realpython.com/media/sockets-tcp-flow.1da426797e37.jpg)



# Echo Client and Server

## Echo server

```python
#!/usr/bin/env python3

import socket

HOST = '127.0.0.1'  # Standard loopback interface address (localhost)
PORT = 28137        # Port to listen on (non-privileged ports are > 1023)

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.bind((HOST, PORT))
    s.listen()
    conn, addr = s.accept()
    with conn:
        print('Connected by', addr)
        while True:
            data = conn.recv(1024)
            if not data:
                break
            conn.sendall(data)

```

## Echo Client

```python
#!/usr/bin/env python3

import socket

HOST = '127.0.0.1'  # The server's hostname or IP address
PORT = 65432        # The port used by the server

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.connect((HOST, PORT))
    s.sendall(b'Hello, world')
    data = s.recv(1024)

print('Received', repr(data))

```



# How the client and server communicate with each other

+ Using the loopback interface(IPv4 address 127.0.0.1 or IPv6 address ::1)

![loopback interface](https://files.realpython.com/media/sockets-loopback-interface.44fa30c53c70.jpg)



+ Using the Ethernet interface

![Ethernet interface](https://files.realpython.com/media/sockets-ethernet-interface.aac312541af5.jpg)



# Multi-Connection Client and Server

## selects()

**selects()** allows you to check for I/O completion on more than one socket. So you can call *select()* to see which sockets have I/O ready for reading and/or writing.    

**Python provides the *selectors* module**. We can create a server and client that handles multiple connections using a *selector* object from the *selectors* module.    

## Multi-Connection Server

```python
#!/usr/bin/env python3

import sys
import socket
import selectors
import types

sel = selectors.DefaultSelector()


def accept_wrapper(sock):
    conn, addr = sock.accept()  # Should be ready to read
    print("accepted connection from", addr)
    conn.setblocking(False)
    data = types.SimpleNamespace(addr=addr, inb=b"", outb=b"")
    events = selectors.EVENT_READ | selectors.EVENT_WRITE
    sel.register(conn, events, data=data)


def service_connection(key, mask):
    sock = key.fileobj
    data = key.data
    if mask & selectors.EVENT_READ:
        recv_data = sock.recv(1024)  # Should be ready to read
        if recv_data:
            data.outb += recv_data
        else:
            print("closing connection to", data.addr)
            sel.unregister(sock)
            sock.close()
    if mask & selectors.EVENT_WRITE:
        if data.outb:
            print("echoing", repr(data.outb), "to", data.addr)
            sent = sock.send(data.outb)  # Should be ready to write
            data.outb = data.outb[sent:]


if len(sys.argv) != 3:
    print("usage:", sys.argv[0], "<host> <port>")
    sys.exit(1)

host, port = sys.argv[1], int(sys.argv[2])
lsock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
lsock.bind((host, port))
lsock.listen()
print("listening on", (host, port))
lsock.setblocking(False)
sel.register(lsock, selectors.EVENT_READ, data=None)

try:
    while True:
        events = sel.select(timeout=None)
        for key, mask in events:
            if key.data is None:
                accept_wrapper(key.fileobj)
            else:
                service_connection(key, mask)
except KeyboardInterrupt:
    print("caught keyboard interrupt, exiting")
finally:
    sel.close()
```

+ `lsock.setblocking(False)` configures the socket in non-blocking mode. Calls made to this socket will no longer *block*. When it's used with `sel.select()`, we can wait for events on one or more sockets and then read and write data when it's ready.    

+ `sel.register()` registers the socket to be monitored with `sel.select()` for events you're interested in. *data* is used to store whatever arbitrary data you'd like along with the socket. It's returned when *select()* returns. We'll use *data* to keep track of what's been sent and received on the socket.     

+ `sel.select(timeout=None)` blocks until there are sockets ready for I/O. It returns a list of *(key, events)* tuples, one for each socket. 

  ​	*key* is a *SelectorKey* namedtuple that contains a *fileobj* attribute. `key.fileobj` is the socket object, and *mask* is an event mask of the operations that are ready. 

  ​	If `key.data` is *None*, then we know it's from the listening socket and we need to accept the connection. If `key.data` is not *None*, then we know it's a client socket that's already been accepted, and we need to service it.

+ *accept_wrapper()*

  ​	Since the listening socket was registered for the event `selector.EVENT_READ`, it should be ready to read. We call `sock.accept()` and then immediately call `conn.setblocking(False)` to put the socket in non-blocking mode.    

  ​    Since we want to known when the client connection is ready for reading and writing, we set `events = selectors.EVENT_READ | selectors.EVENT_WRITE`

+ *service_connection()*

  ​	*key* is the *namedtuple* returned from `select()` that contains the socket object(fileobj) and data object. *mask* contains the events that are ready.     

  ​    If the socket is ready for reading, then `mask & selectors.EVENT_READ` is true, and `sock.recv()` is called. Any data that's read is appended to `data.outb` so it can be sent later.    
  
  ​    If no data is received, which means the client has closed their socket, the server should call `sel.unregister()` and then close the socket.

## Multi-Connection Client



```python
#!/usr/bin/env python3

import sys
import socket
import selectors
import types

sel = selectors.DefaultSelector()
messages = [b"Message 1 from client.", b"Message 2 from client."]


def start_connections(host, port, num_conns):
    server_addr = (host, port)
    for i in range(0, num_conns):
        connid = i + 1
        print("starting connection", connid, "to", server_addr)
        sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        sock.setblocking(False)
        sock.connect_ex(server_addr)
        events = selectors.EVENT_READ | selectors.EVENT_WRITE
        data = types.SimpleNamespace(
            connid=connid,
            msg_total=sum(len(m) for m in messages),
            recv_total=0,
            messages=list(messages),
            outb=b"",
        )
        sel.register(sock, events, data=data)


def service_connection(key, mask):
    sock = key.fileobj
    data = key.data
    if mask & selectors.EVENT_READ:
        recv_data = sock.recv(1024)  # Should be ready to read
        if recv_data:
            print("received", repr(recv_data), "from connection", data.connid)
            data.recv_total += len(recv_data)
        if not recv_data or data.recv_total == data.msg_total:
            print("closing connection", data.connid)
            sel.unregister(sock)
            sock.close()
    if mask & selectors.EVENT_WRITE:
        if not data.outb and data.messages:
            data.outb = data.messages.pop(0)
        if data.outb:
            print("sending", repr(data.outb), "to connection", data.connid)
            sent = sock.send(data.outb)  # Should be ready to write
            data.outb = data.outb[sent:]


if len(sys.argv) != 4:
    print("usage:", sys.argv[0], "<host> <port> <num_connections>")
    sys.exit(1)

host, port, num_conns = sys.argv[1:4]
start_connections(host, int(port), int(num_conns))

try:
    while True:
        events = sel.select(timeout=1)
        if events:
            for key, mask in events:
                service_connection(key, mask)
        # Check for a socket being monitored to continue.
        if not sel.get_map():
            break
except KeyboardInterrupt:
    print("caught keyboard interrupt, exiting")
finally:
    sel.close()
```

+ `start_connections(host, port, num_conns)`
  + *num_conns* is read from the command-line, which is the number of connections to create to the server. Each socket is set to non-blocking mode.    
  + `connect_ex()` is used instead of `connect()` since `connect()` would immediately raise a *BlockingIOError* exception. `connect_ex()` initially returns an error indicator, `error.EINPROGRESS`, instead of raising an exception while the connection is in progress. Once the connection is completed, the socket is ready for reading and writing and is returned as such by `select()`.    
+ `service_connection(key, mask)`



# Application Client and Server



When using TCP, you're reading from a continuous stream of bytes.    

When bytes arrive at your socket, there are network buffers involved. Once you've read them, they need to be saved somewhere. Calling `recv()` again reads the next stream of bytes available from the socket.    

What this means is that you'll be reading from the socket in *chunks*. You need to call `recv()` and save the data in a buffer until you've read enough bytes to have a complete message that makes sense to your application.    

It's up to you to define and keep track of where the message boundaries are. As far as the TCP sockets is connected, it's just sending and receiving raw bytes to and from the network. It knows nothing about what those raw bytes mean.    

This bring us to define an application-layer protocol. What's an application-layer protocol? Put simply, your application will send and receive messages. These messages are application's protocol.    

In other words, the length and format you choose for these messages define the semantics and behavior of your application. When you're reading bytes with `recv()`, you need to keep up with how many bytes were read and figure out where the message boundaries are.    

How is this done? One way is to always send fixed-length messages. If you're always the same size, then it's easy. When you've read that number of bytes into a buffer, then you know you have one complete message.    

However, using fixed-length message is inefficient for small messages where you'd need to use padding to fill them out. Also, you're still left with the problem of what to do about data that doesn't fit into one message.    

Approach to this is that prefix messages with a header that includes the content length as well as any other fields we need. By doing this, we'll only need to keep up with the header. Once we've read the header, we can process it to determine the length of the message's content and then read that number of bytes to consume it.    



























