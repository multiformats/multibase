# Base8

The multibase base8 prefix is the character `7`. This spec is derived from
RFC4648.

## Encoding

Map each 3 bytes (8-bit word), with the most significant bit on the left side
(big-endian), to 8 3-bit words as follows:

`[⁰b₇₆₅₄₃₂₁₀, ¹b₇₆₅₄₃₂₁₀, ²b₇₆₅₄₃₂₁₀]`

`[⁰b₇₆₅, ⁰b₄₃₂, ⁰b₁₀¹b₇, ¹b₆₅₄, ¹b₃₂₁, ¹b₀²b₇₆, ²b₅₄₃, ²b₂₁₀]`

Then map their values as big-endian unsigned ints to their chars:

```
000 → '0'
001 → '1'
...
111 → '7'
```

Treat missing subbits as zero and optionally pad.

## Decoding

Map chars to the following 3-bit words:

```
'0' → 000
'1' → 001
... 
'7' → 111
```

Then map each 8 3-bit words, with the most significant bit on the left side
(big-endian), to 3 bytes (8-bit word) as follows:

`[⁰b₂₁₀, ¹b₂₁₀, ²b₂₁₀, ³b₂₁₀, ⁴b₂₁₀, ⁵b₂₁₀, ⁶b₂₁₀, ⁷b₂₁₀]`

`[⁰b₂₁₀¹b₂₁₀²b₂₁, ²b₀³b₂₁₀⁴b₂₁₀⁵b₂, ⁵b₁₀⁶b₂₁₀⁷b₂₁₀]`

If there are not enough bits to complete the last 8-bit word then drop that last
incomplete 8-bit word.
