# lock code

So we got constraints, based on which we gotta find the code. \
Will not lie, I took a look at the solution for this one, but did not hit ctrl c ctrl v. 

the constraints we got are :
1. 291, one of them is correct and in its correct place
2. 245, one is correct but wrongly placed.
3. 463, two are correct but wrongly placed.
4. 578, nothing is correct.
5. 569, one is correct but wrongly placed.

How they did it was, for condition 1, either 2,9 or 1 is in its correct place. \
so (code[0] == 2 or code[1] == 9 or code[2] == 1)

For condition 2,5, we move the digits around in the code \
and if they are in their correct spot we add 1 \
in the end the count we should get should be equal to one, since only one number is correct.

similarly for condition 3 \
the count we should get for numbers in right spots should be 2

for condition 4, we gotta make all possible combinations of [5,7,8] not possible

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

# sum of bros in correct spots should be 1
solver.add(Sum([If(code[i] == 5,1,0) for i in range(3)]) +
           Sum([If(code[i] == 6,1,0) for i in range(3)]) +
           Sum([If(code[i] == 9,1,0) for i in range(3)]) == 1)
solver.add(And(code[0] != 5, code[1] != 6, code[2] != 9))

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

if solver.check() == sat:
    model = solver.model()
    solution = [model[code[i]] for i in range(3)]
else:
    solution = "LMAO"

print(solution)
```

![image](https://github.com/IC3lemon/z3-summer-training/assets/150153966/505b2d5f-ba2f-4cba-970b-9b9a96596ede)


