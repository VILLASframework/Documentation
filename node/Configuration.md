# Configuration {#node-config}

The VILLASnode configuration consists of a a single file.

For a collection of example configuration files see: <https://git.rwth-aachen.de/acs/public/villas/VILLASnode/tree/develop/etc>.

At the top level, the configuration file consists of these sections:

- Global
- Logging
- WebServer
- Nodes
- Paths

# Global {#node-config-global}

The global section consists of some global configuration parameters:

## stats (float) {#node-config-stats}

Specifies the rate at which statistics about the active paths will be periodically printed to the screen.

Setting this value to 5, will print 5 lines per second.

A line includes information such as:

  - Source and Destination of path
  - Messages received
  - Messages sent
  - Messages dropped

## affinity (integer) {#node-config-affinity}

Restricts the exeuction of the daemon to certain CPU cores.
This technique, also called 'pinning', improves the determinism of the server by isolating the daemon processes on exclusive cores.

## priority (integer) {#node-config-priority}

Adjusts the scheduling priority of the deamon processes.
By default, the daemon uses a real-time optimized FIFO scheduling algorithm.

## hugepages (integer) {#node-config-hugepages}

The number of hugepages which will be reservered by the system.

See: <https://www.kernel.org/doc/Documentation/vm/hugetlbpage.txt>

## name (string) = hostname {#node-config-name}

By default the `name` of a VILLASnode instance is equal to the hostname of the machine it is running on.
Some node types are using this name to identify themselves agains their remotes.
An example is the `ngsi` node type which adds a metadata attribute `source` to its updates.

# Logging {#node-config-logging}

```
logging = {
	file = "/var/log/villas-node.log";
	level = 5;
	facilities = "socket,log,mem";	// log socket node-type, log and memory sub-system
}
```

## logging.level (integer) {#node-config-logging-level}

`log.level` expects a positive integer number which adjusts the verbosity of debug messages.
Use this with care! Producing a lot of IO might decrease the performance of the server.
Omitting this setting or setting it to zero will disable debug messages completely.

## logging.file (path) {#node-config-logging-file}

Write all log messages to a file.

## logging.facilities (string) = "all" {#node-config-logging-facilities}

`log.facilities` is a comma-separated expression which selects the active logging facilities.
Each of the facilities can be prefixed with an exclamation mark in order to exclude it.

Examples:

```
facilities = "nodes,!websocket";	// log all node-type but not the websocket node-type
facilities = "all,!nodes";	// log everything but not the node-types
facilities = "socket,log,mem";	// log socket node-type, log and memory sub-system
```

The following logging facilities are available:

| Facility	| Description |
|:-		    |:- |
| `pool` 	| Memory Pool for fixed size allocations |
| `queue` 	| Multiple-producer / Multiple-consumer queue |
| `config` 	| Configuration parser |
| `hook` 	| Hook sub-system |
| `path` 	| Send / Receive path |
| `node` 	| Node-types |
| `mem` 	| Memory management |
| `web` 	| Web sub-system |
| `api` 	| Remote API |
| `log` 	| Logging sub-system |
| `vfio` 	| Virtual Function Input/Output sub-system for access to PCI devices |
| `pci` 	| PCI device detection |
| `xil` 	| Xilinx drivers for FPGA components |
| `tc` 		| Linux traffic control for network emulation |
| `if` 		| Linux network interfaces |
| `advio`	| Network IO |
| `io`		| Standard IO Formats |
| `socket` 	| BSD Socket node-type |
| `file` 	| File node-type |
| `fpga` 	| VILLASfpga node-type |
| `ngsi` 	| FIWARE NGSI node-type |
| `websocket` 	| WebSocket node-type |
| `opal` 	| OPAL-RT node-type |
| `ib`		| Infiniband node-type |

# HTTP / WebSocket server {#node-config-http}

```
http = {
	enabled = true,					# Do not listen on a port if false

	htdocs = "/villas/web/socket/",			# Root directory of internal webserver
	port = 80					# Port for HTTP connections

	ssl_cert = "/etc/ssl/certs/mycert.pem";
	ssl_private_key= "/etc/ssl/private/mykey.pem";
}
```

## http.enabled (boolean) = true {#node-config-http-enabled}

When set to `false` the VILLASnode daemon will not listen on a port for servering HTTP and WebSocket requests.

## http.htdocs (string: path) = "/usr/share/villas/node/web"  {#node-config-http-htdocs}

The location of of static files served by the HTTP / WebSocket server.

## http.port (integer)  = 80 {#node-config-http-port}

The TCP port number on which HTTP / WebSocket server.

## http.ssl_cert (string: path) {#node-config-ssl_cert}

The public x509 certificate used for server-side SSL encryption.

## http.ssl_private_key (string: path) {#node-config-ssl_private_key}

The private x509 certificate used for server-side SSL encryption.

# Nodes {#node-config-node}

The node section is a **directory** of nodes (clients) which are connected to the VILLASnode instance.
The directory is indexed by the name of the node:

```
nodes = {
	"sintef_node" = {
		type = "socket",
		vectorize = 10,
		hooks = (
			{
				type = "decimate",
				ratio = 10
			}
		),

		builtin = true,
		samplelen = 64,

		signals = (
			{ name = "Va", unit = "Volts", format = "float", enabled = true },
			{ name = "Vb", unit = "Volts", format = "float", enabled = true },
			{ name = "Vc", unit = "Volts", format = "float", enabled = true },
		)

		# type specific settings follow here.
	}
}
```

There are different type of nodes available. But all types have the following settings in common:

## type (string: "socket" | "fpga" | ...) {#node-config-node-type}

`type` specifies which interface should be used for this node.

For a complete list of supported node-types run `villas node --help`.

In addition to the node settings described in this section, every node type has its own specific settings.
Take a look at the @ref node-types page for details.

## out.netem (directory) {#node-config-node-netem}

Some socket based node-types support network emulation.

For more details see @ref node-netem

## in.vectorize (integer) = 1 {#node-config-node-vectorize}

This setting allows to send multiple samples in a single message to the destination nodes.

The value of this setting determines how many samples will be combined into one packet.

## in.hooks (list of objects: hooks) {#node-config-node-hook}

A list of hook functions which will be executed for each sample which is processed by this path.

Please consult the @ref node-concept-hook chapter of this documentation for details.

## builtin (boolean) = true {#node-config-node-builtin}

By default, each node and paths has a couple of default hooks attached to them.
With this setting the attachment of built-in hooks can be disabled.

## in.signals (list of objects: signals) = () {#node-config-node-signals}

Each node should define a list of signals which it **receives**.

There are three ways to specify the input signals of a node:

### Format string

The easiest way to specify the signals, is by using a format string.
The format string consists of one ore more characters which define the type for the signal corresponding to the position of the character in the string.

| Character | Type            | Setting for full and list mode |
|:---       |:---             |:---
| `f`       | Floating point | "float" |
| `b`       | Boolean | "boolean" |
| `i`       | Integer | "integer" |
| `c`       | Complex Floating point | "complex" |

Optionally, the characters can be prefixed by an integer for easier repitition.

The following signal definition defines 15 signals, of which the first 12 are floating point and the last 3 are integer values.

```
nodes = {
	test_node = {
		in = {
			signals = "12f3i"
		}
	}
}
```

### Simple

The simple way just specifies the number of signals and their type:

```
nodes = {
	test_node = {
		in = {
			signals = {
				count = 12
				type = "float"
			}
		}
	}
}
```

### List

The list mode allows you to specify properties of each signal individually by providing a list of signal definitions:

```
nodes = {
	test_node = {
		in = {
			signals = (
				{ name = "signal1", type = "float" },
				{ name = "signal2", type = "integer" }
			)
		}
	}
}
```

Each signal is described by the following settings:

#### in.signals[].name (string) = undefined {#node-config-node-signals-name}

A name which describes the signal.

#### in.signals[].unit (string) = undefined {#node-config-node-signals-unit}

The unit of the signal. E.g. `V`, `A`, `Rad`.

#### in.signals[].format (string: "float" | "integer" | "boolean" | "complex" | "auto") = auto {#node-config-node-signals-format}

The data-type of the signal.

#### in.signals[].enabled (boolean) = true {#node-config-node-signals-enabled}

Signals can be disabled which causes them to be ignored.

# Paths {#node-config-path}

The path section consists of a **list** of paths:

```
paths = (
	{
		in = [
			"rtds.data[0-5]",
			"web.data[0-2]"
		],
		out = [
			"broker",
			"opal"
		],

		reverse = false,
		mode = "any",
		mask = [ "rtds" ],
		rate = 100,
		original_sequence_no = false,

		hooks = (
			{
				type = "print"
			},
			{
				type = "ts"
			}
		)
	}
)
```

Every path can have the following settings:

## in (list of strings: node-names | mapping expression) {#node-config-path-in}

The `in` settings expects the name of one or more source nodes or mapping expressions.

## out (list of strings: node-names) {#node-config-path-out}

The `out` setting expects the name of one or more destination nodes.
Each sample which is processed by the path will be sent to each of the destination nodes.

## enabled (boolean) {#node-config-path-enabled}

The optional `enabled` setting can be used to temporarily disable a path.
If omitted, the path is enabled by default.

## reverse (boolean) {#node-config-path-reverse}

By default, the path is unidirectional. Meaning, that it only forwards samples from the source to the destination.
Sometimes a bidirectional path is needed.
This can be accomplished by setting `reverse` to `true`.

## mode (string) = "any" {#node-config-path-mode}

The mode setting specifies under which condition a path is _triggered_.
A triggered path will multiplex / merge samples from its input nodes and run the configured hook functions on them.
Afterwards the processed and merged samples will be send to all output nodes.

Two modes are currently supported:

- `any`: The path will trigger the path as soon as any of the masked (see @ref node-config-path-mask) input nodes received new samples.
- `all`: The path will trigger the path as soon as all input nodes received at least one new sample.

## mask (list of strings: node-names) = all nodes {#node-config-path-mask}

This setting allows masking the the input nodes which can trigger the path.

See also: @ref node-config-path-mode

## rate (float) = 0 {#node-config-path-rate}

A non-zero value will periodically trigger the path and resend the last sample again.

A value of zero will disable this feature.

## original_sequence_no (boolean) = false {#node-config-path-original_sequence_no}
When this flag is set, the original sequence number from the source node will be used when multiplexing the nodes.

## hooks (list of objects: hooks) {#node-config-path-hooks}

A list of hook functions which will be executed for each sample which is processed by this path.

Please consult the @ref node-concept-hook chapter of this documentation for details.

### hooks[].type (string: "print" | "drop" | ...) {#node-config-hooks-type}

### hooks[].enabled (boolean) = true {#node-config-hooks-enabled}
