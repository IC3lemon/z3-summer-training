# sonda

on running the program it asks you for a magic number.

on dissecting this with ida, u can see this : \
![image](https://github.com/IC3lemon/z3-summer-training/assets/150153966/3746b6b7-097d-4d17-84cb-10cd0b31ef65)

ur magic number i.e. seed has to give `0` on `seed % 17` and needs to be smaller than 20 to not show 'BAD...'

therefore our seed is either 0 or 17, \
i went with 17 because seed was being used more in the program for other things \
for which it being 0 wouldn't have made sense.

so we know our seed = 17 \
now if our seed is correct, this gets executed \
![image](https://github.com/IC3lemon/z3-summer-training/assets/150153966/d61db3df-8a0c-456b-b0ea-a694073f8738)

a bunch of stuff going on here :
- first a string input is taken called s, of length seed, which is the flag
- the srand function is seeded with seed
- then a list of numbers ptr is constructed, whose length is seed (17)
- ptr is constructed based on random numbers, which we can calculate because we know the seed
- now a certain v9 is calculated and summed up, and checked with each character in out passed in string i.e. flag
- only if v9 = string_chars ==> our flag correct

so just find the flag that satisfies all this.

calculated the srand random numbers like this : \
![image](https://github.com/IC3lemon/z3-summer-training/assets/150153966/2878bb30-10ec-49eb-bb58-ee36cf71ec1d)

![image](https://github.com/IC3lemon/z3-summer-training/assets/150153966/9d8edf52-34f9-48b1-acb6-15f1f05e6cb6)

```python
from z3 import *
import random

seed = 17
rand = [1227918265, 3978157, 263514239, 1969574147, 1833982879,
         488658959, 231688945, 1043863911, 1421669753, 1942003127,
         1343955001, 461983965, 602354579, 726141576, 1746455982,
         1641023978, 1153484208, 945487677, 1559964282, 1484758023]
# Create a solver instance
solver = Solver()

ptr = [BitVec(f"ptr{i}", 32) for i in range(17)]
s = [BitVec(f"s{i}", 8) for i in range(17)]

solver.add(len(s) <= seed)
solver.add(ptr[0] == 2*seed + rand[0] % (5*seed))

for i in range(1,seed):
    v5 = ptr[i-1]
    solver.add(ptr[i] == v5 + rand[i]%94 + 33)

for j in range(seed):
    v9 = BitVecVal(0,32)
    for k in range(j+1):
        v9 += ZeroExt(24,s[k]) #converts from str to int, puts in the trailing bits
    solver.add(v9 == ptr[j])

if s.check() == sat:
    model = solver.model()
    print("solution found")
    print(model)

else:
    print("lol no")
```
