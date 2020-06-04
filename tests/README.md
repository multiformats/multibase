Multibase test vectors
======================

This directory contains test vectors that should be used by every implementation to check if it correctly implements the Base encoding as specified by Multibase.

| Filename | String to encode | Description
| -------- | ---------------- | -----------
| `test2.csv` | `yes mani !` | Basic case
| `test4.csv` | `\x00yes mani !` | Leading zero byte
| `test5.csv` | `\x00\x00yes mani !` | Two leading zero bytes (leading zeros are a source of bugs, hence there are two test vectors for them)
| `test6.csv` | `hello world` | Differently cased than expected, must decode without errors
