# lock code

```python
from z3 import *
from itertools import permutations

solver = Solver()
code = [Int(f'code{i}') for i in range(3)]

# either 2,9, or 1 correct
solver.add(Or(code[0] == 2 ,code[1] == 9 ,code[2] == 1))

# sum of bros in correct spots should be 1
solver.add(Sum([If(code[i] == 2, 1, 0) for i in range(3)]) +
           Sum([If(code[i] == 4, 1, 0) for i in range(3)]) +
           Sum([If(code[i] == 5, 1, 0) for i in range(3)]) == 1)
solver.add(And(code[0] != 2, code[1] != 4, code[2] != 5))

# sum of bros in correct spots should be 2
solver.add(Sum([If(code[i] == 4, 1, 0) for i in range(3)]) +
           Sum([If(code[i] == 6, 1, 0) for i in range(3)]) +
           Sum([If(code[i] == 3, 1, 0) for i in range(3)]) == 2)
solver.add(And(code[0] != 4, code[1] != 6, code[2] != 3))

# no bros correct
for i in range(3):
    for j in range(3):
        for k in range(3):
            solver.add(And(code[i] != 5, code[j] != 7, code[k] != 8))

# sum of bros in correct spots should be 1
solver.add(Sum([If(code[i] == 5,1,0) for i in range(3)]) +
           Sum([If(code[i] == 6,1,0) for i in range(3)]) +
           Sum([If(code[i] == 9,1,0) for i in range(3)]) == 1)

if solver.check() == sat:
    model = solver.model()
    solution = "".join(str(x) for x in [model[code[i]] for i in range(3)])
else:
    solution = "LMAO"

print(solution)
```

`3-9-4`
