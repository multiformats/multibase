# multibase

[![](https://img.shields.io/badge/made%20by-Protocol%20Labs-blue.svg?style=flat-square)](http://ipn.io)
[![](https://img.shields.io/badge/project-multiformats-blue.svg?style=flat-square)](http://github.com/multiformats/multiformats)
[![](https://img.shields.io/badge/freenode-%23ipfs-blue.svg?style=flat-square)](http://webchat.freenode.net/?channels=%23ipfs)

> Self identifying base encodings

Multibase is a protocol for distinguishing base encodings and other simple string encodings, and for ensuring full compatibility with program interfaces. It answers the question:

> Given data d encoded into string s, how can I tell what base d is encoded with?

Base encodings exist because transports have restriections, use special in-band sequences, or must be human-friendly. When systems chose a base to use, it is not always clear _which_ base to use, as there are many tradeoffs in the decision. Multibase is here to save programs and programmers from worrying about which encoding is best. It solves the biggest problem: a program can use multibase to take input or produce output in whichever base is desired. The important part is that the value is self-describing, letting other programs elsewhere know what encoding it is using.

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
base16        F, f    highest char in hex
base32        U, u    rfc4648 - highest letter
base32hex     V, v    rfc4648 - highest char
base58flickr  Z       highest char
base58btc     z       highest char
base64        y       rfc4648 highest char
base64url     Y       rfc4648 highest char
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

> Why have digest size as a separate number?

Because otherwise you end up with a function code really meaning "function-and-digest-size-code". Makes using custom digest sizes annoying, and is less flexible.

> Why isn't the size first?

Because aesthetically I prefer the code first. You already have to write your stream parsing code to understand that a single byte already means "a length in bytes more to skip". Reversing these doesn't buy you much.

> Why varints?

So that we have no limitation on functions or lengths. Implementation note: you do not need to implement varints until the standard multibase table has more than 127 functions.

> What kind of varints?

An Most Significant Bit unsigned varint, as defined by the [multiformats/unsigned-varint](https://github.com/multiformats/unsigned-varint).

> Don't we have to agree on a table of base encodings?

Yes, but we already have to agree on base encodings, so this is not hard. The table even leaves some room for custom encodings.

## Implementations:

- [go-multibase](//github.com/multiformats/go-multibase)
- [js-multibase](//github.com/multiformats/js-multibase)
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
