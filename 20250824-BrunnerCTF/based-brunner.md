# Description
```
Difficulty: Beginner
Author: Nissen

Brunsviger is just so based, I think I could eat it in any form - from binary to decimal!

Tip: This might require a bit of programming, I would recommend looking into the int() function in Python.
```

# Attachments
encode.py
```
def encode_char(ch: str, base: int) -> str:
    """
    Encode a single character into a string of digits in the given base
    """
    value = ord(ch)
    digits = []
    while value > 0:
        digits.append(str(value % base))
        value //= base

    return "".join(reversed(digits))


with open("flag.txt") as f:
    text = f.read().strip()

# Encode the text with all bases from decimal to binary
for base in range(10, 1, -1):
    text = " ".join(encode_char(ch, base) for ch in text)

with open("based.txt", "w") as f:
    f.write(text)
```

based.txt
```
<a very long binary string>
```

# Solution
Understand that the encode_char encodes a string by converting each character's unicode into another base. \
Then, the script repeats the encoding with the encoded string from base 10 to base 2. \
Thankfully, the process is reversible. Python's built-in int(d, b) function returns an integer given\
a string of digits d and a desired base b. \
Therefore, repeatedly decode each string into higher base and get the flag.\

Below is a script I used to test some stuff.\

```
def decode(digits, base):
  return chr(int(digits, base))


def encode_char(ch: str, base: int) -> str:
    """
    Encode a single character into a string of digits in the given base
    """
    value = ord(ch)
    digits = []
    while value > 0:
        digits.append(str(value % base))
        value //= base


    return "".join(reversed(digits))


text = "hi"


print("Original: " + text + "\n")
for base in range(10,8,-1):
  text = " ".join(encode_char(ch, base) for ch in text)


print("Encoded: " + text + "\n")


for base in range(9, 11):
  text = "".join(decode(ch, base) for ch in text.split(" "))
print("Decoded: " + text + "\n")
```

