# Multibase

[![](https://img.shields.io/badge/made%20by-Protocol%20Labs-blue.svg?style=flat-square)](http://ipn.io)
[![](https://img.shields.io/badge/project-multiformats-blue.svg?style=flat-square)](https://github.com/multiformats/multiformats)
[![](https://img.shields.io/badge/freenode-%23ipfs-blue.svg?style=flat-square)](https://webchat.freenode.net/?channels=%23ipfs)
[![](https://img.shields.io/badge/readme%20style-standard-brightgreen.svg?style=flat-square)](https://github.com/RichardLitt/standard-readme)

> Self-identifying base encodings

Multibase is a protocol for disambiguating the "base encoding" used to express binary data in text formats (e.g., base32, base36, base64, base58, etc.) from the expression alone.

When text is encoded as bytes, we can usually use a one-size-fits-all encoding (UTF-8) because we're always encoding to the same set of 256 bytes (+/- the NUL byte).
When that doesn't work, usually for historical or performance reasons, we can usually infer the encoding from the context.

However, when bytes are encoded as text (using a base encoding), the choice of base encoding (and alphabet, and other factors) is often restricted by the context.
Worse, these restrictions can change based on where the data appears in the text. 
In some cases, we can only use `[a-z0-9]`; in others, we can use a larger set of characters but need a compact encoding. 
This has lead to a large set of "base encodings", almost one for every use-case.
Unlike the case of encoding text to bytes, it is impractical to standardize widely around a single base encoding because there is no optimal encoding for all cases.

As data travels beyond its context, it becomes quite hard to ascertain *which* base encoding of the many possible ones were used; that's where multibase comes in. 
Where the data has been prefixed before leaving its context behind, it answers the question:

> Given binary data `d` encoded into text `s`, what base `b` was used to encode it?

To answer this question, a single code point is prepended to `s` at time of encoding, which signals in that new context which `b` can be used to reconstruct `d`.

## Table of Contents

- [Format](#format)
  - [Multibase Table](#multibase-table)
- [Specifications](#specifications)
- [Status](#status)
  - [Reserved Terms](#reserved-terms)
- [Multibase By Example](#multibase-by-example)
- [FAQ](#faq)
- [Implementations:](#implementations)
- [Disclaimers](#disclaimers)
- [Contribute](#contribute)
- [License](#license)

## Format

The Format is:

```
<base-encoding-code-point><base-encoded-data>
```

Where `<base-encoding-code-point>` is a code representing an entry in the multibase table.

### Multibase Table

The current multibase table is [here](multibase.csv):

```
Unicode,    character,  encoding,           description,                                                    status
U+0000,     NUL,        none,               (No base encoding),                                             reserved
U+0030,     0,          base2,              Binary (01010101),                                              experimental
U+0031,     1,          none,               (No base encoding)                                              reserved
U+0037,     7,          base8,              Octal,                                                          draft
U+0039,     9,          base10,             Decimal,                                                        draft
U+0066,     f,          base16,             Hexadecimal (lowercase),                                        final
U+0046,     F,          base16upper,        Hexadecimal (uppercase),                                        final
U+0076,     v,          base32hex,          RFC4648 case-insensitive - no padding - highest char,           experimental
U+0056,     V,          base32hexupper,     RFC4648 case-insensitive - no padding - highest char,           experimental
U+0074,     t,          base32hexpad,       RFC4648 case-insensitive - with padding,                        experimental
U+0054,     T,          base32hexpadupper,  RFC4648 case-insensitive - with padding,                        experimental
U+0062,     b,          base32,             RFC4648 case-insensitive - no padding,                          final
U+0042,     B,          base32upper,        RFC4648 case-insensitive - no padding,                          final
U+0063,     c,          base32pad,          RFC4648 case-insensitive - with padding,                        draft
U+0043,     C,          base32padupper,     RFC4648 case-insensitive - with padding,                        draft
U+0068,     h,          base32z,            z-base-32 (used by Tahoe-LAFS),                                 draft
U+006b,     k,          base36,             Base36 [0-9a-z] case-insensitive - no padding,                  draft
U+004b,     K,          base36upper,        Base36 [0-9a-z] case-insensitive - no padding,                  draft
U+0052,     R,          base45,             Base45 RFC9285,                                                 draft
U+007a,     z,          base58btc,          Base58 Bitcoin,                                                 final
U+005a,     Z,          base58flickr,       Base58 Flicker,                                                 experimental
U+006d,     m,          base64,             RFC4648 no padding,                                             final
U+004d,     M,          base64pad,          RFC4648 with padding - MIME encoding,                           experimental
U+0075,     u,          base64url,          RFC4648 no padding,                                             final
U+0055,     U,          base64urlpad,       RFC4648 with padding,                                           final
U+0070,     p,          proquint,           Proquint (https://arxiv.org/html/0901.4016),                    experimental
U+0051,     Q,          none,               (no base encoding)                                              reserved
U+002F,     /,          none,               (no base encoding)                                              reserved
U+1F680,    🚀,         base256emoji,       base256 with custom alphabet using variable-sized-codepoints,   experimental
```

**NOTE:** Multibase-prefixes are encoding agnostic. "z" is "z", not 0x7a ("z" encoded as ASCII/UTF-8). In UTF-32, for example, that same "z" would be `[0x7a, 0x00, 0x00, 0x00]` not `[0x7a]`, so detecting and dropping an initial byte of `0x7a` would not suffice to confirm the rest was `base58btc`-encoded bytes; `[0x7a, 0x00, 0x00, 0x00]` would instead be the UTF-32 bytes that correspond to the `z` codepoint for that entry, and the entire byte array would need to be detected and dropped. Also note the difference between `0x00` (codepoint 0 or 0x00) and `0` (codepoint 48 or 0x30).

## Specifications

Below is a list of specs for the underlying base encodings:

- `base2` [Base2 RFC](rfcs/Base2.md)
- `base8` [Base8 RFC](rfcs/Base8.md), similar to [rfc4648](https://datatracker.ietf.org/doc/html/rfc4648.html)
- `base10` [Base10 RFC](rfcs/Base10.md)
- `base36` [Base36 RFC](rfcs/Base36.md)
- `base16*` [RFC4648](https://datatracker.ietf.org/doc/html/rfc4648.html)
- `base32*` (Except for `base32z`) [rfc4648](https://datatracker.ietf.org/doc/html/rfc4648.html)
- `base32z` [Human-oriented base32 spec](https://philzimmermann.com/docs/human-oriented-base-32-encoding.txt)
- `base45` [RFC9285](https://datatracker.ietf.org/doc/html/rfc9285.html)
- `base64*` [RFC4648](https://datatracker.ietf.org/doc/html/rfc4648.html)
- `base58btc` https://datatracker.ietf.org/doc/html/draft-msporny-base58-02
- `base58flickr` https://datatracker.ietf.org/doc/html/draft-msporny-base58-02, but using a different alphabet
- `proquint` [Proquint RFC](rfcs/Proquint.md), which is the [original spec](https://arxiv.org/html/0901.4016) with an added prefix for legibility



## Status

Each multibase encoding has a status:

* reserved - for functional reasons or to avoid collisions with other multi-* registries, this registry cannot accept registrations at this code-point and implementing one unregistered is discouraged for interoperability reasons
* experimental - these encodings have been proposed but are not widely implemented and may be removed.
* draft - these encodings are mature and widely implemented but may not be implemented by all implementations.
* final - these encodings should be implemented by all implementations and are widely used.
* deprecated - this entry will likely be removed and reassigned in the future and it will not likely become a `final` registration

### Reserved Terms

The following codes are _reserved_ and cannot be registered in the `multibase` table. Note that all three of the Unicode entries, expressed as the [unsigned varint] expression of that Unicode code-point in UTF-8, correspond to widely-used entries in the [multiformats registry group] that could create confusions for some legacy systems handling both binary and multibased structures from other multiformats. While technically the multibase registry is not part of the [multiformats registry group], these reservations minimize risk of confusion when composing multiple multiformats in one data system.

* `NUL` (n/a) - Legacy data may be found with null-byte-prefixed binary structures mixed in among multibase-encoded ones in arrays of data, although support for this is no longer mandated by conformant implementations.
* `/` (U+002F) - Separator used by [multiaddr].
* `1` (U+0031) - Base58-encoded identity multihashes used by libp2p peer IDs.
* `Q` (U+0051) - Base58-encoded sha2-256 multihashes used by libp2p/ipfs for peer IDs and CIDv0.

## Multibase By Example

Consider the following encodings of the same binary string:

```
4D756C74696261736520697320617765736F6D6521205C6F2F # base16 (hex)
JV2WY5DJMJQXGZJANFZSAYLXMVZW63LFEEQFY3ZP           # base32
3IY8QKL64VUGCX009XWUHKF6GBBTS3TVRXFRA5R            # base36
TZ9:VDNEDHECDZC+ED944A4FVQEF$DK84%UB21             # base45
YAjKoNbau5KiqmHPmSxYCvn66dA1vLmwbt                 # base58
TXVsdGliYXNlIGlzIGF3ZXNvbWUhIFxvLw==               # base64
```

And consider the same encodings with their multibase prefix

```
F4D756C74696261736520697320617765736F6D6521205C6F2F # base16 F
BJV2WY5DJMJQXGZJANFZSAYLXMVZW63LFEEQFY3ZP           # base32 B
K3IY8QKL64VUGCX009XWUHKF6GBBTS3TVRXFRA5R            # base36 K
RTZ9:VDNEDHECDZC+ED944A4FVQEF$DK84%UB21             # base45 R
zYAjKoNbau5KiqmHPmSxYCvn66dA1vLmwbt                 # base58 z
MTXVsdGliYXNlIGlzIGF3ZXNvbWUhIFxvLw==               # base64 M
```

The base prefixes used are: `F, B, K, R, z, M`.


## FAQ

> Is this a real problem?

Yes. If i give you `"1214314321432165"` is that decimal? or hex? or something else? See also:
- https://en.wikipedia.org/wiki/8-bit_clean
- https://en.wikipedia.org/wiki/MIME#Content-Transfer-Encoding
- http://stackoverflow.com/questions/8571501/how-to-check-whether-the-string-is-base64-encoded-or-not

> Why the strange selection of codes / characters?

The code values are selected such that they are included in the alphabets of the base they represent. 
For example, `f` is the base code for `base16 (hex)`, because `f` is in hex's 16 character alphabet.
Note that most of the alphabets used can be encoded in UTF-8, and most but not all can be encoded in ASCII.
We have yet not found a case needing something else.

> Don't we have to agree on a table of base encodings?

Yes, but we already have to agree on base encodings, so this is not hard.
The table even leaves some room for custom encodings and is intended to work both in contexts where the encodings are known or agreed on and open-world or brownfield contexts where these may vary.

## Implementations:

- [go-multibase](https://github.com/multiformats/go-multibase)
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
- Kotlin
  - [kotlin-multibase](https://github.com/changjiashuai/kotlin-multibase)
  - `multibase` part of Kotlin project [multiformat](https://github.com/erwin-kok/multiformat)
- [zig-multibase](https://github.com/optimism-java/multiformats-zig)
- [Add yours here!](https://github.com/multiformats/multibase/edit/master/README.md)


## Disclaimers

Warning: **obviously multibase changes the first character depending on the encoding**. 
Do not expect the value to be exactly the same. 
Remove the multibase prefix before using the value.

## Contribute

Contributions welcome. 
Please check out [the issues](https://github.com/multiformats/multibase/issues) and reading the [contributing document](https://github.com/multiformats/multiformats/blob/master/contributing.md) for the greater multiformats project before opening your first issue, as the workflow and the relation of multibase to the greater project both benefit from this context.
more information on how we work, and about contributing in general.

If you'd like to switch a project over to multibase, whether by creating a new multibase implementation or building on one of those listed above, please file an issue in this repository using the "Interested in implementing" issue template.
If would also like to reserve a prefix for compatibility, please file a separate issue in this repository using the "New Registration" issue template.

## License

This repository is only for documents. 
All of these are licensed under the [CC-BY-SA 3.0](https://ipfs.io/ipfs/QmVreNvKsQmQZ83T86cWSjPu2vR3yZHGPm5jnxFuunEB9u) license © 2016 Protocol Labs Inc.
Any code is under a [MIT](LICENSE) © 2016 Protocol Labs Inc.

[multiaddr]: https://github.com/multiformats/multiaddr
[multiformats registry group]: https://github.com/multiformats/multicodec/blob/master/table.csv
[unsigned varint]: https://github.com/multiformats/unsigned-varint
[code point]: https://infra.spec.whatwg.org/#code-points
