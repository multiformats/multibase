### Intro

`base32cx` is a base-32 encoding with letter-case checksums inspired by the success of Ethereum’s [EIP55](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-55.md).

It is designed for encoding relatively short byte strings presented as human-skimmable strings, like file hashes or cryptocurrency addresses.

The alphabet maximizes the number of alpha characters to increase the average number of checksum bits per string.

`base32cx` has a variant, `base32ux`, which is the same alphabet without a checksum.

The unchecked variant `base32ux` has the property that a lexical sort of encoded data is a bitwise sort of decoded data, like `base32hex`.

### Alphabet

```
                       base32cx alphabet

                   value:  0,1,2,3,4,5,[6..31]
                encoding:  4,5,6,7,8,9,[A..Z] 
                 lowered:  4,5,6,7,8,9,[a..z] 

value encoding  value encoding  value encoding  value encoding
----- --------  ----- --------  ----- --------  ----- --------
    0 4             8 C (c)        16 K (k)        24 S (s)
    1 5             9 D (d)        17 L (l)        25 T (t)
    2 6            10 E (e)        18 M (m)        26 U (u)
    3 7            11 F (f)        19 N (n)        27 V (v)
    4 8            12 G (g)        20 O (o)        28 W (w)
    5 9            13 H (h)        21 P (p)        29 X (x)
    6 A (a)        14 I (i)        22 Q (q)        30 Y (y)
    7 B (b)        15 J (j)        23 R (r)        31 Z (z)
```

### Checksum

To checksum, take the sha256 of the data to be encoded. Call this hash `CHECK`.

Encode the bytes using the alphabet above. (Hint: It's a drop-in replacement for `base32hex`).

Lowercase the i'th character of encoded string if the (i % 256)‘th bit of `CHECK` is a 0.
Keep it uppercased if it is a 1.

### Example

```
encode("Hello")

    encoding    result      note
   
    base32cx    d5mQSv7j    appears mixed / passes checksum
    base32ux    D5MQSV7j    appears uniform / fails checksum
      none      d5mqsv7j    appears mixed  / fails checksum, uppercased might be base32ux
```

Uppercase letters are chosen for the unchecked variant because the numeric characters are “tall”.
This makes unchecked data appear uniform while checked data appears mixed-height.

### Maximum size

`base32cx` is only defined for byte sequences up to length 2^20 - 1, that is, one byte less than 1 MiB.
It is most likely not an appropriate choice of encoding for larger data.
For completeness, a standard method for hashing large data and applying the checksum in chunks will be specified in the future.
Until then, base32cx is simply not defined if the data to be encoded is longer than 2^20 - 1 bytes.

### Source

The spec was originally posted and is being maintained [here](https://word.site/2019/11/13/base32cx/).
