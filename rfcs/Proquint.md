# PRO-QUINT

For the original proquint specification, see: https://arxiv.org/html/0901.4016 ([/ipfs/bafybeib5jsyi5igjwhi7hzkfebpvnq2ykbwpxeaaxlkyfyxqvcecoao4qa](https://dweb.link/ipfs/bafybeib5jsyi5igjwhi7hzkfebpvnq2ykbwpxeaaxlkyfyxqvcecoao4qa)).

The multibase prefix for proquints is the character `p`. The base encoded data is the encoded data according to the original specification, with an additional `ro-` prefix:

```
<multibase-prefix-character><additional-prefix-characters><proquint-encoded-data>
```

The resulting full prefix for the actual proquint encoded data is `pro-`, making multibase-encoded proquints easily pronouncable.
For example, the proquint encoding of the bytestring `[127, 0, 0, 1]` (the data for the IPv4 address `127.0.0.1`) is `lusab-babad`, so the corresponding multibase-encoded proquint bytestring is:

```
pro-lusab-babad
```

## Odd-byte inputs

The original proquint specification operates on 16-bit chunks and is silent on inputs whose length is not a multiple of two bytes. Multibase requires arbitrary byte sequences to be encodable, so this document specifies the following extension:

When the input has an odd number of bytes, every pair of bytes is encoded as a 5-character `CVCVC` block as usual, and the final byte is encoded as a 3-character `CVC` block representing the high 8 bits of a 16-bit value whose low 8 bits are zero. The trailing `CVC` block is joined to the preceding blocks with `-` in the usual way.

For example, the single byte `[0x21]` (`!`) is encoded as:

```
pro-fah
```

The second consonant in the trailing `CVC` block carries the two least-significant bits of the input byte in its two most-significant bits; its two least-significant bits MUST be zero. Decoders MUST reject any trailing `CVC` block where this is not the case, so that the encoding is canonical and bijective. The four valid trailing consonants are `b`, `h`, `m`, and `s`.
