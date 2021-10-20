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
