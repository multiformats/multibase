# Base10

## Encoding

A byte array is encoded to multibase base10 by prefixing its base10 encoding
with the character `9`.

A byte array is encoded to base10 by concatenating its leading zeros with the
ASCII encoding of the decimal representation of the byte array interpreted as a
big-endian integer.

The leading zeros of a byte array are a byte array of the character `0`, whose
length is equal to the length of consecutive `0x00` bytes in the beginning of
the byte array.

The decimal representation of a byte array is obtained by setting the
accumulator to the value of the first nonzero byte of the array, and then for
every subsequent byte setting a new value for the accumulator to be equal to the
previous value multiplied by 256, plus the value of the current byte. The last
byte of the output array is set to the ASCII character corresponding to the
value of the accumulator modulo 10, and the accumulator is set to the
accumulator divided by 10. The second-to-last byte of the output array is then
set with the same process, iterating until the accumulator is 0.

The multibase base10 encoding of a byte array must not contain any characters
outside the range `0..9` (decimal 48..57; hexadecimal `0x30..0x39`)

The multibase base10 encoding of the byte array `[0x00, 0x01]` is `"901"`

The encoding of `[0x00, 0x00, 0xff]` is `"900255"`

The encoding of `[0x01, 0x00]` is `"9256"`

The encoding of `[0x00, 0x01, 0x00]` is `"90256"`

## Decoding

A multibase base10 encoding is decoded by dropping its first byte
(which must be the character `9`)

A base10 encoding is decoded into a byte array by concatenating its leading null
bytes with the big-endian representation of the encoding interpreted as a
decimal integer.

The leading null bytes of a base10 encoding are a byte array of `0x00` whose
length is equal to the number of `0` characters before a nonzero character in
the base10 encoding.

The big-endian representation of a base10 encoding is obtained by setting the
accumulator to the number corresponding to the first nonzero ASCII numeral in
the encoding, then for each subsequent character multiplying the accumulator by
10 and adding to it the number corresponding to the character.

The number corresponding to a character in the range `0..9` is obtained by
subtracting the decimal value 48 (hexadecimal `0x30`) from the value of the
byte. The character corresponding to a decimal number is obtained by adding 48
to the number.
