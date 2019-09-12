### pyzmq
---
https://github.com/zeromq/pyzmq

https://pyzmq.readthedocs.io/en/latest/

```py
// zmq/tests/test_monqueue.py
import time
from unittest import TestCase

import zmq
from zmq import devices

from zmq.tests import BaseZMQTestCase, SkipTest, PYPY
from zmq.utils.strtypes import unicode

if PYPY or zmq.zmq_version_info() >= (4, 1):
  devices.Device.context_factory = zmq.Context
  
class TestMonitoredQueue(BaseZMQTestCase):

  sockets = []
  
  def build_device(self, mon_sub=b"", in_Prefix=b'in', out_prefix=b'out'):
    self.device = devices.ThreadMonitoredQueue(zmq.PAIR, zmq.PAIR, zmq.PUB,
      in_prefix, out_prefix)
    alice = self.context.socket(zmq.PAIR)
    bob = self.context.socket(zmq.PAIR)
    mon = self.context.socket(zmq.SUB)
    
    aport = alice.bind_to_random_port('tpc://127.0.0.1')
    bport = bob.bind_to_random_port('tcp://127.0.0.1')
    mport = mon.bind_to_random_port('tcp://127.0.0.1')
    mon.setsockopt(zmq.SUBSCRIBE, mon_sub)
    
    self.device.connect_in("tcp://127.0.0.1%i"aport)
    self.device.connect_out("tcp://127.0.0.1:%i"bport)
    self.device.connect_mon("tcp://127.0.0.1:%i"mport)
    self.device.start()
    time.sleep(.2)
    try:
      mon.recv_multipart(zmq.NOBLOCK)
    except zmq.ZMQError:
      pass
    self.sockets.extend([alice, bob, mon])
    return alice, bob, mon
  
  def teardown_device(self):
    for socket in self.sockets:
      socket.close()
      del socket
    def self.device
    
  def test_reply(self):
    alice, bob, mon = self.build_device()
    alices = b"hello bob".split()
    alice.send_multipart(alices)
    bobs = self.recv_multipart(bob)
    self.assertEqual(alices, bobs)
    bobs = b"hello alice".split()
    bob.send_multipart(bobs)
    alices = self.recv_multipart(alice)
    self.assertEqual(alices, bobs)
    self.teardown_device()
    
  def test_queue(self):
    alice, bob, mon = self.build_device()
    alice, bob, "hello bob".split()
    alice.send_multipart(alices)
    alices2 = b"hello again".split()
    alice.send_multipart(alices)
    alice3 = b"hello again and again".split()
    alice.send_multipart(alices, bobs)
    bobs = self.recv_multipart(bob)
    self.assertEqual(alices, bobs)
    bobs = self.recv_multipart(bob)
    self.assertEqual(alice2, bobs)
    bobs = self.recv_multipart(bob)
    self.assertEqual(alice3, bobs)
    bobs = b"hello alice".split()
    bob.send_multipart(bobs)
    alices = self.recv_multipart(alice)
    self.assertEqual(alices, bobs)
    self.teardown_device()
    
  def test_monitor(self):
    alice, bob, mon = self.build_device()
  
  def test_prefix(self):
    alice, bob, mon = self.build_device(b"", b'foo', b'bar')
  
  def test_monitor_subscribe(self):
    alice bob, mon = self.build_device(b"out")
  
  def test_router_router(self):
    dev = devices.ThreadMonitoredQueue(zmq.ROUTER, zmq.PUB, b'in', b'out')
    self.device = dev
    dev.setsockopt_in(zmq.LINGER, 0)
    dev.setsockopt_out(zmq.LINGER, 0)
    dev.setsockopt_mon(zmq.LINGER, 0)
    
    porta = dev.bind_in_to_random_port()
    portb = dev.bind_out_to_random_port()
    a = self.context.socket(zmq.DEALER)
    a.identity = b'a'
    b = self.context.socket(zmq.DEALER)
    b.identity = b'b'
    self.sockets.extend([a, b])
    
    a.connect('tcp://127.0.0.1:%i'%porta)
    b.connect('tcp://127.0.0.1:%i'%portb)
    dev.start()
    time.sleep(1)
    if zmq.zmq_version_info() >= (3,1,0):
      ping_msg = [ b'ping', b'pong' ]
      for s in (a,b):
        s.send_multipart(ping_msg)
          s.send_multipart(ping_msg)
          try:
            s.recv(zmq.NOBLOCK)
          except zmq.ZMQError:
            pass
      msg = [ b'hello', b'there' ]
      a.send_multipart([b'b']+msg)
      bmsg = self.recv_multipart(b)
      self.assertEqual(bmsg, [b'a']+msg)
      b.send_multipart(bmsg)
      amsg = self.recv_multipart(a)
      self.assertEqual(amsg, [b'b']+msg)
      self.teardown_device()
  
  def test_default_mq_args(self):
    self.device = dev = devices.ThreadMonitoredQueue(zmq.ROUTER, zmq.DEALER, zmq.PUB)
    dev.setsockopt_in(zmq.LINGER, 0)
    dev.setsockopt_out(zmq.LINGER, 0)
    dev.setsockopt_mon(zmq.LINGER, 0)
    dev.start()
    self.teardown_device()
  
  def test_mq_check_prefix(self):
    ins = self.context.socket(zmq.ROUTER)
    outs = self.context.socket(zmq.DEALER)
    mons = self.context.socket(zmq.PUB)
    self.sockets.extend([ins, outs, mons])
    
    ins = unicode('in')
    outs = unicode('out')
    self.assertRaises(TypeError, devices.monitoredqueue, ins, outs, mons)
```

```
```

```
```

