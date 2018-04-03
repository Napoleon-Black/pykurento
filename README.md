# Pykurento

## About This Fork
This fork is to support KMS 6.7.X and seems to support new JSON RPC (in general). No examples were updated, only the core code.
What was implemented so far:
*  Everything required for Trickle ICE exchange
*  Better SessionId handling for multisession RPC exchange with KMS
*  Asynchroneous (from websocket receiver thread) KMS message handling to avoid deadlock when doing RPC calls from withing KMS event handler


Pykurento is a [Kurento](http://www.kurento.org/docs/5.0.3/what_is_kurento.html) client written in python and uses the [websocket-client](https://github.com/liris/websocket-client) library for its transport layer.

## Installing

```
pip install git+https://github.com/minervaproject/pykurento.git#egg=pykurento
```

## Usage

Here's a simple example of a loopback pipeline created in a tornado request handler.

```python
from pykurento import KurentoClient, media

kurento = KurentoClient("ws://localhost:8888/kurento")

class LoopbackHandler(tornado.web.RequestHandler):
  def get(self):
    with open("loopback.html","r") as f:
      self.finish(f.read())

  def post(self):
    sdp_offer = self.request.body
    pipeline = kurento.create_pipeline()
    wrtc_pub = media.WebRtcEndpoint()
    sdp_answer = wrtc_pub.process_offer(sdp_offer)
    wrtc_pub.connect(wrtc_pub)
    self.finish(str(sdp_answer))
```

[Source for loopback.html](https://github.com/minervaproject/pykurento/blob/master/examples/views/loopback.html)


## Developing

### Source and deps

```
git clone https://github.com/minervaproject/pykurento
cd ./pykurento
pip install -r examples/requirements.txt
```

### Running the examples

```
./examples/app.py
```

or

```
PORT=8080 ./examples/app.py
```

There is an assumption in the examples that your KMS address is localhost:8888. The easiest way during development to make this work is to setup an ssh tunnel to your media server.

```
ssh -nNT -i <identity file> -L 8888:localhost:8888 <user>@<server address>
```

## License
As with Kurento, this client is released under the terms of [LGPL version 2.1](http://www.gnu.org/licenses/lgpl-2.1.html) license.

