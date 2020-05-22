# Base36

The multibase base36 prefix is the character `k` or `K`. The digit-alphabet
consists of 0..9 and then the case insensitive range a..z for the values 10..35

## Encoding

A byte array is encoded to base36 by:

1. Counting the number of leading 0 bytes (Z).
2. Interpreting the rest of the byte array as a big-endian unsigned integer (N).
3. Concatenating a length Z string of '0' characters with the base36
   representation of N.

A byte array is encoded to multibase base36 by prefixing its base36 encoding
with the character `k`.

## Decoding

A multibase base36 encoded string is decoded by first dropping the multibase
prefix (which must be `k` or `K`).

The remaining characters are then converted to a byte array by:

1. Counting the number of leading '0' characters (Z).
2. Interpreting the rest of the character sequence as a base36 unsigned integer
   (N).
3. Concatenating a length Z array of NULL (0x00) bytes with N encoded as a
   big-endian unsigned integer.

## Examples

Byte Array <-> Base36 Multibase:

| Bytes | ==  | LC Base36 | OR | UC Base36 |
|---|---|---|---|---|
| `[0x00, 0x01]`       | == | `"k01"`   | | `"K01"`   |
| `[0x00, 0x00, 0xff]` | == | `"k0073"` | | `"K0073"` |
| `[0x01, 0x00]`       | == | `"k74"`   | | `"K74"`   |
| `[0x00, 0x01, 0x00]` | == | `"k074"`  | | `"K074"`  |
