# Lab 3: Sending data to node from standard input  {#node-guide-lab3}

@image html villas_pipe1.svg height=150px

The `villas pipe` tool can be used to send and receive sample to / from a node.

The following command sends a single _sample_ to node `udp_node1`.

```
$ # start tcpdump to capture traffic in background
$ tcpdump -i lo udp port 12001

$ # send 10 values to udp_node1
$ villas signal -v 3 -r 10 -l 10 mixed | villas pipe lab3.conf udp_node1

$ # stop tcpdump
$ kill %1
```

We can verify this by using Wireshark or `tcpdump`:

@htmlonly
<asciinema-player rows="25" cols="500" poster="npt:0:1"  src="recordings/terminal/villas_pipe_tcpdump.json">
@endhtmlonly

@image html wireshark_udp.png Wireshark capture of UDP traffic width=50%
