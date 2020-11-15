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

def accept_wrapper(sock):
    conn, addr = sock.accept()  #Should be ready to read
    print('accepted connection from', addr)
    conn.setblocking(False)
    data = types.SimpleNamespace(addr=addr, inb=b'', out=b'')
    events = selectors.EVENT_READ | selectors.EVENT.WRITE 
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
            print('echoing', repr(data.outb), 'to', data.addr)
            sent = sock.send(data.outb)   # Should be ready to write
            data.outb = data.outb[sent:]
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
messages = [b'Message 1 from client.', b'Message 2 from client.']

def start_connections(host, port, num_conns):
    server_addr = (host, port)
    for i in range(0, num_conns):
        connid = i + 1
        print('starting connection', connid, 'to', server_addr)
        sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        sock.setblocking(False)
        sock.connect_ex(server_addr)
        events = selectors.EVENT_READ | selectors.EVENT_WRITE
        data = types.SimpleNamespace(connid=connid,
                                     msg_total=sum(len(m) for m in messages),
                                     recv_total=0,
                                     messages=list(messages),
                                     outb=b'')
        sel.register(sock, events, data)
        
def service_connection(key, mask):
    
```

+ *num_conns* is read from the command-line, which is the number of connections to create to the server. Each socket is set to non-blocking mode.    
+ `connect_ex()` is used instead of `connect()` since `connect()` would immediately raise a *BlockingIOError* exception. `connect_ex()` initially returns an error indicator, `error.EINPROGRESS`, instead of raising an exception while the connection is in progress. Once the connection is completed, the socket is ready for reading and writing and is returned as such by `select()`.





















