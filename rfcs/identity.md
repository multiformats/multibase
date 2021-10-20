# Identity

The multibase identity prefix is the character non-printable ASCII/UTF-8 character with codepoint 0x00. Note that this is different from the multibase prefix 0 listed for base2, which is the ASCII/UTF-8 character "0" with codepoint 0x30.


## Encoding

A byte array `b` is encoded by converting it to the Unicode string `s` having as its UTF-8 bytes the byte array `b` prefixed with a single zero byte.

Below is a minimal implementation in Python, for clarification:

```py
def encode_identity(b: bytes) -> str:
    utf8_bytes = b"\x00"+b
    return utf8_bytes.decode("utf-8")
```

## Decoding

A Unicode string `s` is decoded by obtaining its UTF-8 bytes and dropping the leading byte. The UTF-8 byte array must be non-empty and the leading byte must be zero.

Below is a minimal implementation in Python, for clarification:

```py
def decode_identity(s: str) -> bytes:
    utf8_bytes = s.encode("utf-8")
    if not utf8_bytes or utf8_bytes[0] != 0:
        raise ValueError("String not identity-encoded.")
    return utf8_bytes[1:]
```

## Examples

```py
>>> encode_identity(bytes([0x31, 0x63, 0x57]))
'\x001cW'
>>> decode_identity("\x001cW")
b'1cW'
>>> list(decode_identity("\x001cW"))
[49, 99, 87] # [0x31, 0x63, 0x57]
```
