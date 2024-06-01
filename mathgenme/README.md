# MathGenMe

we got a binary which asks for a 48 char long password, based on which it generates a license key \
we already got a license key, we gotta reverse that to get flag

so first condition, password gotta be 48 chars long

on opening the binary, I found the license key generating function : \
![image](https://github.com/IC3lemon/z3-summer-training/assets/150153966/e52ce126-13ac-482a-a914-3de3330a6b0b)

in this s, is our password, v5 is the license key. \
so whatever going on that loop, our password gotta satisfy that, so those also gonna be constraints.

with these in mind, i cooked this :
```python
from z3 import *

solver = Solver()

password = [BitVec(f"pwd{i}",8) for i in range(48)]

license_key = bytearray.fromhex("04b2fc467e104c0c610e3bf0a009a9f3621905df1997ce0b6cd6a3ea68af4d4deaaf024906f7b259ba32035ac4dad586")
key = [int(x) for x in license_key]
v5 = [BitVec(f"key{i:}",8) for i in range(48)]

# filling in v5
for i in range(len(key)):
    solver.add(v5[i] == key[i])

# adding the main constraints
v4 = 0
for i in range(12):
    solver.add(v5[v4] == 33 * password[v4 + 3] + 89 * password[v4 + 2] + 103 * password[v4 + 1] + 66 * password[v4])
    solver.add(v5[v4 + 1] == 73 * password[v4] + -125 * password[v4 + 1] + -103 * password[v4 + 2] + 51 * password[v4 + 3])
    solver.add(v5[v4 + 2] == 113 * password[v4 + 1] + password[v4 + 3] + 54 * password[v4] + 8 * password[v4 + 2])
    solver.add(v5[v4 + 3] == 25 * password[v4 + 2] + 23 * password[v4 + 3] + 119 * password[v4] + 3 * password[v4 + 1])
    v4 += 4

# flag gotta be in readable range
for i in range(48):
    solver.add(password[i] >= 32)
    solver.add(password[i] <= 126)

if solver.check() == sat:
    model = solver.model()
    flag = ''.join(chr(model[p].as_long()) for p in password)
    print(flag)

else:
    print("hahahahha no.")
```

and yes

![image](https://github.com/IC3lemon/z3-summer-training/assets/150153966/34267f18-3149-4e87-9c2f-09b069dfd4ec)

`pwned{0i_m4t3_y0u_g0t_a_l0ic3nse_f0r_th4t_m4th?}`
