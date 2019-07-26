# Lab 8: Use hook function to modify/filter the data {#node-guide-lab8}

VILLASnode supports hook functions to filter or manipulate samples while their are forwarded.
These functions are in C-code. A plugin mechanism makes it easy for the user to new hook functions.

During this lab we will use the following hook functions:

| Name		| Description |
| :--		| :-- |
| `average` 	| Calculates average over some signals. |
| `cast` 	| Cast signal types. |
| `restart` 	| Call restart hooks for current path. |
| `ts` 		| Overwrite origin timestamp of samples with receive timestamp. |
| `stats` 	| Collect statistics for the current path. |
| `decimate` 	| Downsamping by integer factor. |
| `dp` 	| Transform to/from dynamic phasor. |
| `ebm` 	| Energy based metric. |
| `fix` 	| Fix received data by adding missing fields. |
| `fix_ts` 	| Update timestamps of sample if not set. |
| `gate` 	| Skip samples only if an enable signal is under a specified threshold. |
| `jitter` 	| Hook to calculate jitter between GTNET-SKT GPS timestamp and Villas node NTP timestamp. |
| `limit_rate` 	| Limits the rate of sending signals. |
| `skip_first` 	| Skip the first samples. |
| `stats_send` 	| Send path statistics to another node. |
| `drop` 		| Drop messages with reordered sequence numbers. |
| `convert` 	| Convert message from / to floating-point / integer. |
| `scale` 	| Scale signals by a factor and add offset. |
| `shift_seq` 	| Shift sequence number of samples. |
| `map` 		| Remap values and / or add header, timestamp values to the sample. |
| `print` 	| Print the message to stdout. |
| `shift_ts` 	| Shift timestamps of samples. |

# How to configure a hook function

A hook function can be used in two ways:

- With the `villas hook` to process samples from standard streams.
- With the `villas node` daemon.

When used with `villas hook` we can pass options to the hook function as optional parameters to the invocation of `villas hook`:

```bash
$ villas hook [hook_name] [parameter1] [parameter2] ... [parameterN]
```

@image html villas_hook.svg height=150px

When used with `villas node` the hook is configured as part of a path:

@includelineno node/etc/labs/lab8.conf

## Chaining

Hook  functions are chainable:

```bash
$ villas hook fix_ts | villas hook skip_first samples=10
```

@image html villas_hook_chained.svg height=170px

# Examples

## Skip the first 10 seconds (skip_first)

```bash
$ villas signal sine | villas hook skip_first -o seconds=10
```

## Skip the first 1000 samples (skip_first)

```bash
$ villas signal sine | villas hook skip_first -o samples=1000
```
## Calculate average over first five signals (average)

```bash
$ villas signal sine | villas hook average -o offset=0 -o signals=0,1,2,3,4
```

## Shift the timestamps of all samples 10 seconds into the future (shift_ts)

```bash
$ villas signal sine | villas hook shift_ts mode=origin offset=10.0
```
## Caste signal type (cast)

```bash
$ villas signal -l 10 -r 10 -F 3 -v 1 random > signals_mixed.dat
$ villas-hook cast -o new_name=test -o new_unit=V -o new_type=integer -o signal=1 < signals_mixed.dat

```
## Transformation to dynamic phasor (dp)

```bash
$ villas signal -l 10 -r 10 -F 3 sine > dp_in.dat
$ villas hook dp -o inverse=true -o f0=3 -o rate=10 -o signal=0 -o harmonics=0,1,3,5,7 < dp_out.dat >dp_in2.dat

```
## Limit the rate of sending signals (limit_rate)

```bash
$ villas-signal -r 1000 -l 1000 -n sine > limit_rate_in.dat
$ villas-hook -o rate=10 -o mode=origin limit_rate < limit_rate_in.dat > limit_rate_out.dat

```
## Scale signals by a factor and add offset (scale)

```bash
$ villas signal -l 10 -r 10 -F 3 sine > scale_in.dat
$ villas-hook scale -o scale=100 -o offset=55 -o signal=signal0 < scale_in.dat > scale_out.dat

```

Every sample has three timestamps associated with it:

- `origin`
- `received`
- `sent`

Use the `mode` parameter to select the timestamp which should be shifted.

## Override the timestamp with the current time (ts, fix_ts)

```bash
$ echo "123.456(1) 1.2 3.4 5.6" | villas hook ts
```

@htmlonly
<asciinema-player rows="7" cols="500" poster="npt:0:1"  src="recordings/terminal/villas_hook_ts.json">
@endhtmlonly

There is another related hook function called `fix_ts` which will only overwrite the the timestamp if the source has not provided one (timestamp must is `0.0`).

```bash
$ echo "0.0(1) 1.2 3.4 5.6" | villas hook fix_ts
```

@htmlonly
<asciinema-player rows="12" cols="500" poster="npt:0:1"  src="recordings/terminal/villas_hook_fix_ts.json">
@endhtmlonly

## Reduce the rate by a factor (decimate)

This invocation reduces the sending  rate by a factor of 10.
The resulting rate is: `1000 / 10 = 100`.

```bash
$ villas signal -r 1000 sine | villas hook decimate -o ratio=10
```

@htmlonly
<asciinema-player rows="25" cols="500" poster="npt:0:1"  src="recordings/terminal/villas_hook_decimate.json">
@endhtmlonly

## Convert values between floating point / integer representation (convert)

This invocation convertes values 5-8 to a integer number with a gain factor of 1000:

```bash
$ villas signal sine | villas hook convert 'mode="fixed" mask=0xf0 scale=1000.0'
```

## Print samples to the screen (print)

```bash
$ villas signal sine | villas hook print > /dev/null
```

**Note:** `villas hook` by default prints the processed stream of samples already to the screen. Therefore, every sample appears now twice on the screen. This hook only makes sense when used together with `villas none`.

## Remap values and add special header fields as data (map)

```bash
$ villas signal -v 4 sine | villas hook map 'mapping=[ "data[3]", "data[2]", "data[1]", "data[0]", "hdr.sequence", "ts.origin" ]'
```

@htmlonly
<asciinema-player rows="12" cols="500" poster="npt:0:1"  src="recordings/terminal/villas_hook_map.json">
@endhtmlonly

# Implement your own hook function

Have a look at the following skeleton code: <https://git.rwth-aachen.de/acs/public/villas/VILLASnode/blob/develop/plugins/hooks/example_hook.c>
