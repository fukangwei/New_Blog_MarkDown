---
title: zmq模块
categories: Python语法
abbrlink: f2458143
date: 2018-12-27 17:42:58
---
&emsp;&emsp;服务器端代码如下：<!--more-->

``` python
import time
import json
import zmq
​
context = zmq.Context()
socket = context.socket(zmq.REP)
socket.bind("tcp://*:5555")
​
while True:
    message = socket.recv()  # Wait for next request from client
    request_body = json.loads(message)
    print("Received request: %s" % message)
    # Do some work
    time.sleep(0.001)
    socket.send(b"World")  # Send reply back to client
```

&emsp;&emsp;客户端代码如下：

``` python
import zmq
import json
​
context = zmq.Context()
print("Connecting to hello world server...")
socket = context.socket(zmq.REQ)
socket.connect("tcp://localhost:5555")
​
for request in range(10):  # Do 10 requests, waiting each time for a response
    print("Sending request %s ..." % request)
    request_body = {'command': 'list', 'type': 'command'}
    socket.send_string(json.dumps(request_body))
    message = socket.recv()  # Get the reply
    print("Received reply %s [ %s ]" % (request, message))
```