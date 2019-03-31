# Node-types {#node-types}

Every server needs clients which act as sinks / sources for simulation data.
In case of VILLASnode these clients are called _nodes_.
Every node is an instance of a node-type.
VILLASnode currently supports the following node-types:

| Type                            | Loopback Latency | Sync  | Read | Write | Vectorize | State      | Comments |
| :--                             | :--              | :--   | :--  | :--   | :--       | :--        | :-- |
| @subpage node-type-signal       | ?                | AIO   | yes  | no    | 1         | stable     | |
| @subpage node-type-loopback     | ?                | AIO   | yes  | yes   | unlimited | stable     | |
| @subpage node-type-file         | ?                | AIO   | yes  | yes   | unlimited | stable     | |
| @subpage node-type-socket       | ?                | AIO   | yes  | yes   | unlimited | stable     | |
| @subpage node-type-websocket    | ?                | AIO   | yes  | yes   | unlimited | stable     | |
| @subpage node-type-shmem        | ?                | Poll  | yes  | yes   | unlimited | stable     | |
| @subpage node-type-zeromq       | ?                | AIO   | yes  | yes   | unlimited | stable     | |
| @subpage node-type-nanomsg      | ?                | AIO   | yes  | yes   | unlimited | stable     | |
| @subpage node-type-amqp         | ?                | AIO   | yes  | yes   | unlimited | stable     | |
| @subpage node-type-mqtt         | ?                | AIO   | yes  | yes   | unlimited | stable     | |
| @subpage node-type-influxdb     | N/A              | N/A   | no   | yes   | unlimited | stable     | |
| @subpage node-type-test-rtt     | ?                | AIO   | yes  | yes   | unlimited | stable     | Virtual node-type |
| @subpage node-type-comedi       | ?                | Poll  | yes  | yes   | unlimited | beta       | Support for a wide range of Analog/Digital Input/Output cards |
| @subpage node-type-infiniband   | ?                | ?     | yes  | yes   | unlimited | beta       | |
| @subpage node-type-fpga         | ?                | ?     | yes  | yes   | ?         | beta       | |
| @subpage node-type-iec61850-8-1 | ?                |       | yes  | yes   | 1         | alpha      | |
| @subpage node-type-iec61850-9-2 | ?                |       | yes  | yes   | 1         | beta       | |
| @subpage node-type-opal         | ?                | Poll  | yes  | yes   | 1         | deprecated | |
| @subpage node-type-ngsi         | ?                | AIO   | yes  | yes   | unlimited | stable | Use WebSockets for Live data |
| @subpage node-type-uldaq        | ?                | Poll  | yes  | no    | unlimited | stable     | |
| @subpage node-type-rtp          | ?                | Poll  | yes  | yes   | ?         | beta       | |
| @subpage node-type-ethercat     | ?                | Poll  | yes  | yes   | ?         | alpha      | |
| @subpage node-type-stats        | ?                | Poll  | yes  | no    | 1         | stable     | |

@image html VILLASnode_interfaces.svg width=100%