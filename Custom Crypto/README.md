# Custom Crypto

honestly couldve just backtracked this and done it the crypto way, but this needs z3 work.

```python
from z3 import *

# Given encrypted output
encrypted_output = [28, 24, 1, 9, 9, 19, 93, 93, 94, 2, 26, 13, 6, 92, 61, 11, 15, 39, 91, 91, 20, 28, 54, 8, 17, 89, 23, 61]
key_length = 4
flag_length = len(encrypted_output)

# Known part of the flag
known_prefix = "pwned"

solver = Solver()

# our bois
flag_chars = [BitVec(f'flag_{i}', 8) for i in range(flag_length)]
key_chars = [BitVec(f'key_{i}', 8) for i in range(key_length)]

# Constraints for the known prefix of the flag
for i, c in enumerate(known_prefix):
    solver.add(flag_chars[i] == ord(c))

for i in range(int(round(flag_length / key_length))):
    for p in range(key_length):
        idx = i * key_length + p
        if idx < flag_length:
            offset = i
            solver.add((flag_chars[idx] + offset) ^ key_chars[p] == encrypted_output[idx])

# key and flag characters should be in printable range
for k in key_chars:
    solver.add(k >= 32, k <= 126)

for f in flag_chars:
    solver.add(f >= 32, f <= 126)

if solver.check() == sat:
    model = solver.model()
    decrypted_flag = ''.join(chr(model[flag_chars[i]].as_long()) for i in range(flag_length))
    print(decrypted_flag)

else:
    print("lololno")

```

![image](https://github.com/IC3lemon/z3-summer-training/assets/150153966/90479ccc-f04e-4871-98a0-276cb92c2173)

`pwned{100ks_g0Od_D03snT_w0rK}`
