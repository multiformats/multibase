# Base2

## Encoding

A byte array (or a string represented by a byte array, eg. UTF-8) is encoded to
multibase base2 by prefixing the character `0` to the encoding of the byte array
in base2. The length of the multibase base2 encoding of a byte array of length
_k_ is _8k+1_ bytes.

The encoding of the byte array in base2 is obtained by concatenating the base2
representations of each byte in the array, in the order the bytes are in the
array.

The base2 representation of a byte is a byte array of length 8 in big-endian
order, where each byte of the array is set to the character `1`, if the
corresponding bit in the byte is set, and the character `0` if the corresponding
bit is unset.

The character `0` means the hexadecimal value `0x30`
(ASCII character `0`, decimal value 48).

The character `1` means the hexadecimal value `0x31`
(ASCII character `1`, decimal value 49).

For example, the base2 representation of the ASCII character `Y`, or the
hexadecimal value `0x59` is `"01011001"`. The corresponding multibase base2
encoding of the character `Y` is `"001011001"`

The base2 encoding of any byte array must not contain any other bytes apart from
the characters `0` and `1`.

The entire process of encoding the byte array `[0x58, 0x59, 0x60]`
is as follows:

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
first byte (which must be the character `0`), dividing the remaining byte array
to _k_ sections of length _8_, and mapping to each byte in an output buffer of
_k_ bytes the binary value of the corresponding section.

The binary value of a byte section is obtained by taking the bytes of the
section in big-endian order, and setting the corresponding bit in the output
byte if the byte is `1`, and leaving the bit unset if `0`.

For example, the binary value of the byte section `"00110111"` is `0x37`.

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
for some _k_, may be decoded by dropping the first byte, and then prefixing to
the encoding the smallest number of bytes that makes the length of the encoding
be canonical, and then decoding it as if it were in the canonical base2 form.
For example, the non-canonical encoding `"011010"` may be decoded like the
canonical base2 encoding `"000" + "11010"` to get `0x1a`. A standard multibase
implementation must not output non-canonical encoding.
