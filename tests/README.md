Multibase test vectors
======================

This directory contains test vectors that should be used by every implementation to check if it correctly implements the Base encoding as specified by Multibase.

| Filename | String to encode | Description
| -------- | ---------------- | -----------
| `test1.csv` | `Decentralize everything!!` | Non alphanumeric characters
| `test2.csv` | `yes mani !` | Non alphanumeric characters
| `test3.csv` | `hello world` | ASCII letters only
| `test4.csv` | `\x00yes mani !` | Leading zero byte
| `test5.csv` | `\x00\x00yes mani !` | Two leading zero bytes
| `test6.csv` | `hello world` | Differently cased than expected, must decode without errors
