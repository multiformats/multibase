# multibase (WIP)

[![](https://img.shields.io/badge/made%20by-Protocol%20Labs-blue.svg?style=flat-square)](http://ipn.io)
[![](https://img.shields.io/badge/project-multiformats-blue.svg?style=flat-square)](http://github.com/multiformats/multiformats)
[![](https://img.shields.io/badge/freenode-%23ipfs-blue.svg?style=flat-square)](http://webchat.freenode.net/?channels=%23ipfs)

> Self identifying base encodings

Multibase is a protocol for distinguishing base encodings and other simple string encodings, and for ensuring full compatibility with program interfaces. It answers the question:

> Given data d encoded into string s, how can I tell what base d is encoded with?

Base encodings exist because transports have restrictions, use special in-band sequences, or must be human-friendly. When systems chose a base to use, it is not always clear _which_ base to use, as there are many tradeoffs in the decision. Multibase is here to save programs and programmers from worrying about which encoding is best. It solves the biggest problem: a program can use multibase to take input or produce output in whichever base is desired. The important part is that the value is self-describing, letting other programs elsewhere know what encoding it is using.

## Format

The Format is:

```
<varint-base-encoding-code><base-encoded-data>
```

Where `<varint-base-encoding-code>` is used according to the multibase table. Note that varints (bases above 127) are not yet supported, but planned.

### Multibase Table v1.0.0-RC (semver)

The current multibase table is [here](multibase.csv):

```
encoding      codes   name
base1         1       unary tends to be 11111
base2         0       binary has 1 and 0
base8         7       highest char in octal
base10        9       highest char in decimal
base16        F, f    highest char in hex
base32        B, b    rfc4648 - highest letter
base32hex     V, v    rfc4648 - highest char
base58flickr  Z       highest char
base58btc     z       highest char
base64        m       rfc4648 highest char
base64url     u       rfc4648 highest char
```

These encodings are being considered:

```
base128
base256ascii  X       ascii
base-emoji    😎      base emoji
base65536     ᔰ       base65536
utf8
utf16
```

## Multibase By Example

Consider the following encodings of the same binary string:

```
4D756C74696261736520697320617765736F6D6521205C6F2F # base16 (hex)
JV2WY5DJMJQXGZJANFZSAYLXMVZW63LFEEQFY3ZP           # base32
YAjKoNbau5KiqmHPmSxYCvn66dA1vLmwbt                 # base58
TXVsdGliYXNlIGlzIGF3ZXNvbWUhIFxvLw==               # base64
```

And consider the same encodings with their multibase prefix

```
F4D756C74696261736520697320617765736F6D6521205C6F2F # base16 F
UJV2WY5DJMJQXGZJANFZSAYLXMVZW63LFEEQFY3ZP           # base32 U
zYAjKoNbau5KiqmHPmSxYCvn66dA1vLmwbt                 # base58 z
yTXVsdGliYXNlIGlzIGF3ZXNvbWUhIFxvLw==               # base64 y
```

The base prefixes used are: `F, U, z, y`.


## FAQ

> Is this a real problem?

Yes. If i give you `"1214314321432165"` is that decimal? or hex? or something else? See also:
- https://en.wikipedia.org/wiki/8-bit_clean
- https://en.wikipedia.org/wiki/MIME#Content-Transfer-Encoding
- http://stackoverflow.com/questions/8571501/how-to-check-whether-the-string-is-base64-encoded-or-not

> Why the strange selection of codes / characters?

The code values are selected such that they are included in the alphabets of the base they represent. For example, `F` is the base code for `base16 (hex)`, because `F` is in hex's 16 character alphabet. Note that the alphabets here are ASCII or UTF8 compliant. We have not found a case needing something else.

> Why varints?

So that we have no limitation on functions or lengths. Implementation note: you do not need to implement varints until the standard multibase table has more than 127 functions.

> What kind of varints?

An Most Significant Bit unsigned varint, as defined by the [multiformats/unsigned-varint](https://github.com/multiformats/unsigned-varint).

> Don't we have to agree on a table of base encodings?

Yes, but we already have to agree on base encodings, so this is not hard. The table even leaves some room for custom encodings.

## Implementations:

- [go-multibase](//github.com/multiformats/go-multibase)
- [js-multibase](//github.com/multiformats/js-multibase)
- [cs-multibase](//github.com/tabrath/cs-multibase)
- [Add yours here!](//github.com/multiformats/multibase/edit/master/README.md)


## Disclaimers

Warning: **obviously multibase changes the first byte(s) depending on the encoding**. Do not expect the value to be exactly the same. Remove the multibase prefix before using the value.

## Maintainers

Captain: [@jbenet](https://github.com/jbenet).

## Contribute

Contributions welcome. Please check out [the issues](https://github.com/multiformats/multibase/issues).

Check out our [contributing document](https://github.com/multiformats/multiformats/blob/master/contributing.md) for more information on how we work, and about contributing in general. Please be aware that all interactions related to multiformats are subject to the IPFS [Code of Conduct](https://github.com/ipfs/community/blob/master/code-of-conduct.md).

Small note: If editing the Readme, please conform to the [standard-readme](https://github.com/RichardLitt/standard-readme) specification.

## License

[MIT](LICENSE)
