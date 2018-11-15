# Base10

The multibase base10 prefix is the character `9`.

## Encoding

A byte array is encoded to multibase base10 by prefixing its base10 encoding
with the character `9`.

A byte array is encoded to base10 by:

1. Counting the number of leading 0 bytes (Z).
2. Interpreting the rest of the byte array as a big-endian unsigned integer (N).
3. Concatenating a length Z string of '0' characters with the decimal
   representation of N.

## Decoding

A multibase base10 encoding is decoded by first dropping the multibase prefix
(which must be `9`).


The remaining characters are then converted to a byte array by:

1. Counting the number of leading '0' characters (Z).
2. Interpreting the rest of the character sequence as a base10 unsigned integer
   (N).
3. Concatenating a length Z array of NULL (0x00) bytes with N encoded as a
   big-endian unsigned integer.

## Examples

Byte Array <-> Base10 Multibase:

* `[0x00, 0x01]` <-> `"901"`
* `[0x00, 0x00, 0xff]` <-> `"900255"`
* `[0x01, 0x00]` <-> `"9256"`
* `[0x00, 0x01, 0x00]` <-> `"90256"`
