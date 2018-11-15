# Base2

The multibase base2 prefix is the character `0`. Canonical multibase-base2
encoded data will always be `8k+1` characters long.

## Encoding

A byte array is encoded to multibase base2 by prefixing its base2 encoding with
the character `0`.

A byte array is encoded to base2 by concatenating the base2 representations of
each byte in the array, in order.

The base2 representation of a byte is a byte array of length 8 in big-endian
order, where each byte of the array is set to the character `1`, if the
corresponding bit in the byte is set, and the character `0` if the corresponding
bit is unset.

For example, `[0x58, 0x59, 0x60]` can be converted to multibase base2 as
follows:

```
map each byte to the base2 representation:
  ["01011000", "01011001", "01011010"]

concatenate:
  "010110000101100101011010"

prefix with '0':
  "0010110000101100101011010"
```

## Decoding

The canonical format of multibase base2 encoding is decoded by dropping the
multibase prefix (which must be the character `0`), dividing the remaining
character sequence into _k_ sections of length _8_, then converting each section
into it's corresponding byte value.

The byte value of each section is obtained by mapping each '0' or '1' character
to a 0 or 1 bit in the resulting byte (MSB first).

For example, the byte value of `"00110111"` is `0x37`.

The process, with input `"00100000101000010"`:

```
drop the leading zero:
  "0100000101000010"
divide to sections:
  ["01000001", "01000010"]
allocate output buffer:
  [0x00, 0x00]
set the correct bits:
  [0x41, 0x42] or "AB"
```

A non-canonical encoding of multibase base2 whose length is not equal to _8k+1_
characters for some _k_ may be canonicalized by left-padding the string with '0'
characters (until the length is _8k+1_). The string can then be decoded normally.

For example, the non-canonical encoding `"011010"` may be canonicalized to
`"000011010"` (decoded as `[0x1a]`). A standard multibase implementation must
output base2 in the *canonical* encoding only.
