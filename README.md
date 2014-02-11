#async-websocket

Event-based websocket server based on Twisted

## Requirement

  * Python 2.7+
  * [Twisted](https://twistedmatrix.com/trac/):

## How to use

  $ python server.py

  Will run the websocket server on port 9999

## API
  - `websocket.Protocol.onConnect()`: Callback when the client is connected
  - `websocket.Protocol.onDisconnect()`: Callback when the client is disconnected
  - `websocket.Protocol.onMessage(msg)`: Callback when the client receive a message 
  - `websocket.Protocol.sendMessage(msg)`: Send a message to the client
  - `websocket.Protocol.users`: Dictionnary (self = self.clients[self.id]) off all the clients connected to the server
  - `websocket.Protocol.id`: Client UUID4 id

## Default Implementation

```python

  from twisted.internet.protocol import Factory
  from twisted.internet import reactor
  import websocket

  class ClientWebSocket(websocket.Protocol):

    def onConnect(self):
      for k, c in self.users.items():
        c.sendMessage("%s connected" % self.id)

    def onDisconnect(self):
      for k, c in self.users.items():
        c.sendMessage("%s disconnected" % self.id)

    def onMessage(self, msg):
      for _id, user in  self.users.items():
        user.sendMessage(msg)


  class WebSocketFactory(Factory):
    
    def __init__(self):
      self.users = {}
    
    def buildProtocol(self, addr):
      return ClientWebSocket(self.users)


  reactor.listenTCP(9999, WebSocketFactory())
  reactor.run()

```