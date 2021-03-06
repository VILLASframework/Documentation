# Lab 9: Network emulation and statistics {#node-guide-lab9}

@image html villas_hook_stats.svg height=130px

The `stats` hook can be used to collect statistics about the co-simulation interface like:

- One way delay (OWD)
- Packet loss
- Packet reordering
- Sending rate

```bash
$ villas signal -r 1000 -l 10000 sine | villas hook -o verbose=true -o warmup=3000 stats
```

## With network emulation

@image html villas_hook_stats_netem.svg height=150px

@includelineno node/etc/labs/lab9_netem.conf

In  the first terminal:

```bash
$ villas signal -r 1000 sine | villas pipe etc/lab9_netem.conf udp_node1
```

In a second terminal:

```bash
$ villas pipe etc/lab9_netem.conf udp_node1 -x > delayed_data.dat
```

After a few seconds, press Ctrl-C to stop the processing. Now we can analyze the delay distribution of the received data:

```bash
$ villas hook -o verbose=true -o warmup=1000 stats < delayed_data.dat > /dev/null
```

@htmlonly
<asciinema-player rows="30" cols="500" poster="npt:0:1"  src="recordings/terminal/villas_hook_stats.json">
@endhtmlonly

For more details see @ref node-netem
