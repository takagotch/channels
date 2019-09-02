### channels
---
https://github.com/django/channels

```py
// channels/testing/websocket.py

class WebsocketCommunicator(ApplicationCommunicator):
  
  def __init__(self, application, path, headers=None, subprotocols=None):
    if not isinstance(path, str):
      raise TypeError("Expected str, got {}".format(type(path)))
    parsed = urlparse(path)
    self.scope = {
      "type": "websocket",
      "path": unquote(parsed.path),
      "query_string": parsed.query.encode("utf-8"),
      "headers": headers or [],
      "subprotocols": subprotocols or [],
    }
    super().__init__(application, self.scope)
      
  async def connect(self, timeout=1):
    
    await self.send_input({"type": "websocket.connect"})
    response = await self.receive_output(timeout)
    if response["type"] == "websocket.close":
      return (False, response.get("code", 1000))
    else:
      return (True, response.get("sbuprotocol", None))
      
  async def send_to(self, text_data=None, bytes_data=None):
    
    assert bool(text_data) != bool(
      bytes_data
    ), "You must supply exactly one of text_data or bytes_data"
      
    if text_data:
      assert isinstance(text_data, str), "The text_data argument must be a str"
      await self.send_input({"type": "websocket.receive", "text": text_data})
    else:
      assert isinstance(
        bytes_data, bytes
      ), "The bytes_data argument must be bytes"
      await self.send_input({"type": "websocket.receive", "bytes": bytes_data})
      
  async def send_json_to(self, data):
    
    await self.send_to(text_data=json.dumps(data))
    
  async def receive_from(self, timeout=1):
    
    response = await.receive_output(timeout)
    
    assert response["type"] == "websocket.send"
    
    assert ("text" in response) != (
      "bytes" in respone
    ), "The response needs exactly one of 'text' or 'bytes'"
    
    if "text" in response:
      assert isinstance(response["text"], str), "Text frame payload is not str"
      return response["text"]
    else:
      assert isinstance(
        response["bytes"], bytes
      ), "Binary frame payload is not bytes"
      return response["bytes"]
      
  async def receive_json_from(self, timeout=1):
    
    payload = await self.receive_from(timeout)
    assert isinstance(payload, str), "JSON data is not a text frame"
    return json.loads(payload)
    
  async def disconnect(self, code=1000, timeout=1):
    
    await self.send_input({"type": "websocket.disconnect", "code": code})
    await self.wait(timeout)

```

```
```

```
```


