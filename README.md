# multibase

[![](https://img.shields.io/badge/made%20by-Protocol%20Labs-blue.svg?style=flat-square)](http://ipn.io)
[![](https://img.shields.io/badge/project-multiformats-blue.svg?style=flat-square)](https://github.com/multiformats/multiformats)
[![](https://img.shields.io/badge/freenode-%23ipfs-blue.svg?style=flat-square)](https://webchat.freenode.net/?channels=%23ipfs)
[![](https://img.shields.io/badge/readme%20style-standard-brightgreen.svg?style=flat-square)](https://github.com/RichardLitt/standard-readme)

> Self identifying base encodings

Multibase is a protocol for disambiguating the encoding of base-encoded (e.g.,
base32, base36, base64, base58, etc.) binary appearing in text.

When text is encoded as bytes, we can usually use a one-size-fits-all encoding
(UTF-8) because we're always encoding to the same set of 256 bytes (+/- the NUL
byte). When that doesn't work, usually for historical or performance reasons, we
can usually infer the encoding from the context.

However, when bytes are encoded as text (using a base encoding), the base choice
of base encoding is often restricted by the context. Worse, these restrictions
can change based on where the data appears in the text. In some cases, we can
only use `[a-z0-9]`. In others, we can use a larger set of characters but need a
compact encoding. This has lead to a large set of "base encodings", one for
every use-case. Unlike when encoding text to bytes, we can't just standardize
around a single base encoding because there is no optimal encoding for all
cases.

Unfortunately, it's not always clear *what* base encoding is used; that's where
multibase comes in. It answers the question:

> Given binary data d encoded into text s, what base b was used to encode it?

To answer this question, a binary prefix `dp` is added to `d` that renders (upon
b-encoding) as a one-codepoint prefix, `sp` (note: most entries only concern
themselves with UTF-8 encodings where one codepoint = one character).  This
prefix `sp` is the "code" that makes `b` visible in `s`.

## Table of Contents

- [multibase](#multibase)
  - [Table of Contents](#table-of-contents)
  - [Format](#format)
    - [Multibase Table](#multibase-table)
  - [Reserved](#reserved)
  - [Status](#status)
  - [Multibase By Example](#multibase-by-example)
  - [FAQ](#faq)
  - [Implementations:](#implementations)
  - [Disclaimers](#disclaimers)
  - [Contribute](#contribute)
  - [License](#license)

## Format

The Format is:

```
<base-encoding-character><base-encoded-data>
```

Where `<base-encoding-character>` is used according to the multibase table.

### Multibase Table

The current multibase table is [here](multibase.csv):

```
encoding, code,prefix (UTF-8), description,                                                  status, comments
identity,          NUL, 0x00,  8-bit binary (encoder and decoder keeps data unmodified),     default,
base2,             0,   0x30,  binary (01010101),                                            candidate
base8,             7,   0x37,  octal,                                                        draft
base10,            9,   0x39,  decimal,                                                      draft
base16,            f,   0x66,  hexadecimal,                                                  default
base16upper,       F,   0x46,  hexadecimal,                                                  default
base32hex,         v,   0x76,  rfc4648 case-insensitive - no padding - highest char,         candidate
base32hexupper,    V,   0x56,  rfc4648 case-insensitive - no padding - highest char,         candidate
base32hexpad,      t,   0x74,  rfc4648 case-insensitive - with padding,                      candidate
base32hexpadupper, T,   0x54,  rfc4648 case-insensitive - with padding,                      candidate
base32,            b,   0x62,  rfc4648 case-insensitive - no padding,                        default
base32upper,       B,   0x42,  rfc4648 case-insensitive - no padding,                        default
base32pad,         c,   0x63,    rfc4648 case-insensitive - with padding,                      candidate
base32padupper,    C,   0x43,    rfc4648 case-insensitive - with padding,                      candidate
base32z,           h,   0x68,    z-base-32 (used by Tahoe-LAFS),                               draft
base36,            k,   0x6b,    base36 [0-9a-z] case-insensitive - no padding,                draft
base36upper,       K,   0x4b,    base36 [0-9a-z] case-insensitive - no padding,                draft
base58btc,         z,   0x7a,    base58 bitcoin,                                               default
base58flickr,      Z,   0x5a,    base58 flicker,                                               candidate
base64,            m,   0x6d,    rfc4648 no padding,                                           default
base64pad,         M,   0x4d,    rfc4648 with padding - MIME encoding,                         candidate
base64url,         u,   0x75,    rfc4648 no padding,                                           default
base64urlpad,      U,   0x55,    rfc4648 with padding,                                         default
proquint,          p,   0x70,    PRO-QUINT https://arxiv.org/html/0901.4016,                   draft
base256emoji,      🚀 (Unicode U+1F680),  0xF09F9A80,    base256 with custom alphabet using variable-sized-codepoints, draft
```

**NOTE:** Multibase-prefixes are encoding agnostic, and conformance is tested by the prefix code in the string, not the binary prefix, however encoded. I.e., the code is the `z` codepoint (in BTC-alphabet base 58), not `0x7a` (the binary that becomes the `z` encoded in ASCII/UTF-8). Note that the same binary, in UTF-32, would be `[0x7a, 0x00, 0x00, 0x00]` to produce the same `z` with the base and alphabet.

## Reserved

The following codes are _reserved_ for (backwards) compatibility with existing systems.

* `/` - Separator used by [multiaddr](https://github.com/multiformats/multiaddr).
* `1` - Base58 encoded identity multihashes used by libp2p peer IDs.
* `Q` - Base58 encoded sha2-256 multihashes used by libp2p/ipfs for peer IDs and CIDv0.

If you'd like to switch a project over to multibase and would also like to
reserve a prefix for compatibility, please file an issue.

## Status

Each multibase encoding has a status:

* draft - these encodings have been proposed but are not widely implemented and may be removed.
* candidate - these encodings are mature and widely implemented but may not be implemented by all implementations.
* default - these encodings should be implemented by all implementations and are widely used.

## Multibase By Example

Consider the following encodings of the same binary string:

```
4D756C74696261736520697320617765736F6D6521205C6F2F # base16 (hex)
JV2WY5DJMJQXGZJANFZSAYLXMVZW63LFEEQFY3ZP           # base32
3IY8QKL64VUGCX009XWUHKF6GBBTS3TVRXFRA5R            # base36
YAjKoNbau5KiqmHPmSxYCvn66dA1vLmwbt                 # base58
TXVsdGliYXNlIGlzIGF3ZXNvbWUhIFxvLw==               # base64
```

And consider the same encodings with their multibase prefix

```
F4D756C74696261736520697320617765736F6D6521205C6F2F # base16 F
BJV2WY5DJMJQXGZJANFZSAYLXMVZW63LFEEQFY3ZP           # base32 B
K3IY8QKL64VUGCX009XWUHKF6GBBTS3TVRXFRA5R            # base36 K
zYAjKoNbau5KiqmHPmSxYCvn66dA1vLmwbt                 # base58 z
MTXVsdGliYXNlIGlzIGF3ZXNvbWUhIFxvLw==               # base64 M
```

The base prefixes used are: `F, B, K, z, M`.


## FAQ

> Is this a real problem?

Yes. If i give you `"1214314321432165"` is that decimal? or hex? or something else? See also:
- https://en.wikipedia.org/wiki/8-bit_clean
- https://en.wikipedia.org/wiki/MIME#Content-Transfer-Encoding
- http://stackoverflow.com/questions/8571501/how-to-check-whether-the-string-is-base64-encoded-or-not

> Why the strange selection of codes / characters?

The code values are selected such that they are included in the alphabets of the base they represent. For example, `f` is the base code for `base16 (hex)`, because `f` is in hex's 16 character alphabet. Note that the alphabets can be encoded in UTF8, and most can be encoded in ASCII. We have not found a case needing something else.

> Don't we have to agree on a table of base encodings?

Yes, but we already have to agree on base encodings, so this is not hard. The table even leaves some room for custom encodings.

## Implementations:

- [go-multibase](https://github.com/multiformats/go-multibase)
- [kotlin-multibase](https://github.com/changjiashuai/kotlin-multibase)
- [js-multibase](https://github.com/multiformats/js-multibase)
- [cs-multibase](https://github.com/tabrath/cs-multibase)
- [rust-multibase](https://github.com/multiformats/rust-multibase)
- [java-multibase](https://github.com/multiformats/java-multibase)
- [py-multibase](https://github.com/multiformats/py-multibase)
- [haskell-multibase](https://github.com/multiformats/haskell-multibase)
- [net-ipfs-core](https://github.com/richardschneider/net-ipfs-core)
- [elixir-multibase](https://github.com/nocursor/ex-multibase)
- [scala-multibase](https://github.com/fluency03/scala-multibase)
- [cpp-multibase](https://github.com/cpp-ipfs/cpp-multibase)
- [ruby-multibase](https://github.com/sleeplessbyte/ruby-multibase)
- [dart-multibase](https://github.com/heacare/dart-multibase)
- [yoclib-multibase-php](https://github.com/yocto/yoclib-multibase-php)
- `multibase` sub-module of Python module [multiformats](https://github.com/hashberg-io/multiformats)
- [Add yours here!](https://github.com/multiformats/multibase/edit/master/README.md)


## Disclaimers

Warning: **obviously multibase changes the first character depending on the encoding**. Do not expect the value to be exactly the same. Remove the multibase prefix before using the value.

## Contribute

Contributions welcome. Please check out [the issues](https://github.com/multiformats/multibase/issues).

Check out our [contributing document](https://github.com/multiformats/multiformats/blob/master/contributing.md) for more information on how we work, and about contributing in general. Please be aware that all interactions related to multiformats are subject to the IPFS [Code of Conduct](https://github.com/ipfs/community/blob/master/code-of-conduct.md).

Small note: If editing the README, please conform to the [standard-readme](https://github.com/RichardLitt/standard-readme) specification.

## License

This repository is only for documents. All of these are licensed under the [CC-BY-SA 3.0](https://ipfs.io/ipfs/QmVreNvKsQmQZ83T86cWSjPu2vR3yZHGPm5jnxFuunEB9u) license © 2016 Protocol Labs Inc. Any code is under a [MIT](LICENSE) © 2016 Protocol Labs Inc.
