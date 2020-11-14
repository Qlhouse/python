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

## Multiple-Connection Server

```python
import selectors

sel = selectors.DefaultSelector()
# ...
# set up the listening socket
lsock = socket.socket(socket.AF_INET, socket.SOC_STREAM)
lsock.bind(host, port)
lsock.listen()
print('listening on', (host, port))
lsock.setblocking(False)
sel.register(lsock, selectors.EVENT_READ, data=None)
# event loop
while True:
    events = sel.select(tiemout=None)
    for key, mask in events:
        if key.data is None:
            accept_wrapper(key.fileobj)
        else:
            service_connection(key, mask)

```

+ `lsock.setblocking(False)` configures the socket in non-blocking mode. Calls made to this socket will no longer *block*. When it's used with `sel.select()`, we can wait for events on one or more sockets and then read and write data when it's ready.    

+ `sel.register()` registers the socket to be monitored with `sel.select()` for 

events you're interested in. *data* is used to store whatever arbitrary data you'd like along with the socket. It's returned when *select()* returns. We'll use *data* to keep track of what's been sent and received on the socket.     

+ `sel.select(timeout=None)` blocks until there are sockets ready for I/O. It returns a list of *(key, events)* tuples, one for each socket. *key* is a *SelectorKey* namedtuple that contains a *fileobj* attribute. `key.fileobj` is the socket object, and *mask* is an event mask of the operations that are ready. If `key.data` is *None*, then we know it's from the listening socket and we need to accept the connection. If `key.data` is not *None*, then we know it's a client socket that's already been accepted, and we need to service it.













