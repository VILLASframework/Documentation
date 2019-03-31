# Decimate sample rate {#hook-type-decimate}

The `decimate` hook reduces the sampling rate by periodically discarding samples.

# Configuration {#node-config-hook-decimate}

## ratio (integer) {#node-config-hook-decimate-ratio}

The decimation ratio. A value of 4 will skip every, but the 4th sample in a row.

## Example

@include node/hooks/decimate.conf