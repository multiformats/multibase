# multibase (WIP)

[![](https://img.shields.io/badge/made%20by-Protocol%20Labs-blue.svg?style=flat-square)](http://ipn.io)
[![](https://img.shields.io/badge/project-multiformats-blue.svg?style=flat-square)](https://github.com/multiformats/multiformats)
[![](https://img.shields.io/badge/freenode-%23ipfs-blue.svg?style=flat-square)](https://webchat.freenode.net/?channels=%23ipfs)
[![](https://img.shields.io/badge/readme%20style-standard-brightgreen.svg?style=flat-square)](https://github.com/RichardLitt/standard-readme)

> Self identifying base encodings

Multibase is a protocol for distinguishing base encodings and other simple string encodings, and for ensuring full compatibility with program interfaces. It answers the question:

> Given data d encoded into string s, how can I tell what base d is encoded with?

Base encodings exist because transports have restrictions, use special in-band sequences, or must be human-friendly. When systems chose a base to use, it is not always clear _which_ base to use, as there are many tradeoffs in the decision. Multibase is here to save programs and programmers from worrying about which encoding is best. It solves the biggest problem: a program can use multibase to take input or produce output in whichever base is desired. The important part is that the value is self-describing, letting other programs elsewhere know what encoding it is using.

## Table of Contents

- [Format](#format)
  - [Multibase Table v1.0.0-RC (semver)](#multibase-table-v100-rc-semver)
- [Multibase By Example](#multibase-by-example)
- [FAQ](#faq)
- [Implementations:](#implementations)
- [Disclaimers](#disclaimers)
- [Maintainers](#maintainers)
- [Contribute](#contribute)
- [License](#license)

## Format

The Format is:

```
<varint-base-encoding-code><base-encoded-data>
```

Where `<varint-base-encoding-code>` is used according to the multibase table. Note that varints (bases above 127) are not yet supported, but planned.

### Multibase Table v1.0.0-RC (semver)

The current multibase table is [here](multibase.csv):

```
encoding,           code, description
identity,           0x00, 8-bit binary (encoder and decoder keeps data unmodified)
base1,              1,    unary (11111)
base2,              0,    binary (01010101)
base8,              7,    octal
base10,             9,    decimal
base16,             f,    hexadecimal
base16upper,        F,    hexadecimal
base32hex,          v,    rfc4648 no padding - highest char
base32hexupper,     v,    rfc4648 no padding - highest char
base32hexpad,       t,    rfc4648 with padding
base32hexpadupper,  T,    rfc4648 with padding
base32,             b,    rfc4648 no padding
base32upper,        B,    rfc4648 no padding
base32pad,          c,    rfc4648 with padding
base32padupper,     C,    rfc4648 with padding
base32z,            h,    z-base-32 (used by Tahoe-LAFS)
base58flickr,       Z,    base58 flicker
base58btc,          z,    base58 bitcoin
base64,             m,    rfc4648 no padding
base64pad,          M,    rfc4648 with padding - MIME encoding
base64url,          u,    rfc4648 no padding
base64urlpad,       U,    rfc4648 with padding
```

These encodings are being considered:

```
base128
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
BJV2WY5DJMJQXGZJANFZSAYLXMVZW63LFEEQFY3ZP           # base32 B
zYAjKoNbau5KiqmHPmSxYCvn66dA1vLmwbt                 # base58 z
MTXVsdGliYXNlIGlzIGF3ZXNvbWUhIFxvLw==               # base64 M
```

The base prefixes used are: `F, B, z, M`.


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
- [rust-multibase](//github.com/multiformats/rust-multibase)
- [java-multibase](//github.com/multiformats/java-multibase)
- [py-multibase](//github.com/multiformats/py-multibase)
- [haskell-multibase](//github.com/multiformats/haskell-multibase)
- [net-ipfs-core](//github.com/richardschneider/net-ipfs-core)
- [elixir-multibase](//github.com/nocursor/ex-multibase)
- [Add yours here!](//github.com/multiformats/multibase/edit/master/README.md)


## Disclaimers

Warning: **obviously multibase changes the first byte(s) depending on the encoding**. Do not expect the value to be exactly the same. Remove the multibase prefix before using the value.

## Maintainers

Captain: [@jbenet](https://github.com/jbenet).

## Contribute

Contributions welcome. Please check out [the issues](https://github.com/multiformats/multibase/issues).

Check out our [contributing document](https://github.com/multiformats/multiformats/blob/master/contributing.md) for more information on how we work, and about contributing in general. Please be aware that all interactions related to multiformats are subject to the IPFS [Code of Conduct](https://github.com/ipfs/community/blob/master/code-of-conduct.md).

Small note: If editing the README, please conform to the [standard-readme](https://github.com/RichardLitt/standard-readme) specification.

## License

This repository is only for documents. All of these are licensed under the [CC-BY-SA 3.0](https://ipfs.io/ipfs/QmVreNvKsQmQZ83T86cWSjPu2vR3yZHGPm5jnxFuunEB9u) license © 2016 Protocol Labs Inc. Any code is under a [MIT](LICENSE) © 2016 Protocol Labs Inc.
