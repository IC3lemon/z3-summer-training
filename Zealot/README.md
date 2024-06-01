# Zealot

the file on running asks u for a passphrase, if its correct u win \
inside, there's the checkpass function which verifies ur flag based on these conditions : \
```c
BOOL8 __fastcall checkpass(_BYTE *a1)
{
  return a1[5] == 84
      && a1[6] == 70
      && *a1 == a1[40]
      && (a1[1] ^ 0x1D) == a1[37]
      && a1[3] == a1[18]
      && a1[18] == a1[32]
      && (char)(a1[2] ^ 0x34) | (a1[5] == 20)
      && a1[8] == 103
      && a1[37] != (a1[5] == 32)
      && a1[21] == 116
      && (char)a1[7] + 2 == (char)a1[41]
      && 32 * (char)a1[8] == 3296
      && a1[17] == 104
      && a1[9] == a1[10]
      && a1[14] == a1[29]
      && a1[29] == a1[39]
      && a1[12] == 95
      && (char)a1[16] == ((((char)a1[12] + 2051495600) & 0x4894482C | 0x4280202) ^ 0x4C2C4251)
      && *a1 == 110
      && ((char)(a1[31] ^ a1[41]) & 0x8E56 ^ 0x6D) == (char)a1[14]
      && (char)a1[23] == ((((char)a1[12] - 903645652) & 0x62486926 | 0x4020040) ^ 0x4602601D)
      && a1[2] == 116
      && (char)a1[27] == ((((char)a1[12] + 802870624) & 0xC0A28043 | 0x395D4B28) ^ 0x39DFCB74)
      && (char)a1[31] == ((((char)a1[12] + 1050330464) & 0xDA04A63F | 0x995980) ^ 0x1A99DDE0)
      && ((char)(a1[24] ^ 0x66) & 0x400001 | 0x64) == (char)a1[26]
      && ((a1[24] ^ 0x62) & 1 | 0x64) == a1[33]
      && ((((char)a1[28] << 25) ^ 0x60000000) & 0x200610 | 0x63) == (char)a1[35]
      && a1[3] == 101
      && a1[28] == 102
      && (char)a1[6] + 34 == (char)a1[17]
      && a1[41] == 125
      && (char)a1[20] == (((char)a1[1] + (char)a1[3]) & 0x8450 ^ 0x40 | 0x6C)
      && a1[36] == a1[19]
      && (char)a1[19] == (char)a1[6] + 27
      && (char)a1[34] != -563555251
      && a1[1] == 105
      && (char)a1[25] == (((char)a1[4] >> 3) & 0x120D ^ 0x69)
      && a1[21] != (a1[37] == 0)
      && (a1[20] ^ 0x4E | 0x50) == a1[15]
      && (a1[20] ^ 0x4E | 0x50) == a1[30]
      && (char)a1[11] == (char)a1[22] - 4LL
      && (char)a1[22] == ((char)(a1[9] ^ a1[12]) & 0xB00B ^ 0x68)
      && a1[30] == a1[15]
      && (char)a1[24] == ((((char)a1[5] - 456630272) & 0x9594815B | 0x4A6B0680) ^ 0xCEEB06B2)
      && a1[9] == 111
      && a1[4] == 67
      && (char)a1[38] == (~(char)a1[13] & (char)a1[20] ^ 9 | 0x68)
      && (char)(a1[13] ^ 0x62) == ((char)a1[20] & 0x4696);
}
```

pretty straightforward, I just put all these constraints into z3 
(a few conditions were causing pain, i just commented those out)

```python
from z3 import *

# Create a Z3 solver instance
solver = Solver()

# Define a1 as an array of BitVecs representing each byte in the input array
a1 = [BitVec(f'a1_{i}', 8) for i in range(56)]

# Add constraints based on the given conditions in the function
constraints = [ a1[5] == 84
      , a1[6] == 70
      , a1[0] == a1[40]
      , (a1[1] ^ 0x1D) == a1[37]
      , a1[3] == a1[18]
      , a1[18] == a1[32]
      #, ((a1[2] ^ 0x34) | 20 == a1[5])
      , a1[8] == 103
      #, a1[37] != (a1[5] == 32)
      , a1[21] == 116
      , a1[7] + 2 == a1[41]
      , 32 * a1[8] == 3296
      , a1[17] == 104
      , a1[9] == a1[10]
      , a1[14] == a1[29]
      , a1[29] == a1[39]
      , a1[12] == 95
      , a1[16] == (((a1[12] + 2051495600) & 0x4894482C | 0x4280202) ^ 0x4C2C4251)
      , a1[0] == 110
      , ((a1[31] ^ a1[41]) & 0x8E56 ^ 0x6D) == a1[14]
      , a1[23] == (((a1[12] - 903645652) & 0x62486926 | 0x4020040) ^ 0x4602601D)
      , a1[2] == 116
      , a1[27] == ((( a1[12] + 802870624) & 0xC0A28043 | 0x395D4B28) ^ 0x39DFCB74)
      ,  a1[31] == ((( a1[12] + 1050330464) & 0xDA04A63F | 0x995980) ^ 0x1A99DDE0)
      , ( (a1[24] ^ 0x66) & 0x400001 | 0x64) ==  a1[26]
      , ((a1[24] ^ 0x62) & 1 | 0x64) == a1[33]
      , ((( a1[28] << 25) ^ 0x60000000) & 0x200610 | 0x63) ==  a1[35]
      , a1[3] == 101
      , a1[28] == 102
      ,  a1[6] + 34 ==  a1[17]
      , a1[41] == 125
      ,  a1[20] == (( a1[1] +  a1[3]) & 0x8450 ^ 0x40 | 0x6C)
      , a1[36] == a1[19]
      ,  a1[19] ==  a1[6] + 27
      ,  a1[34] != -563555251
      , a1[1] == 105
      ,  a1[25] == (( a1[4] >> 3) & 0x120D ^ 0x69)
      #, a1[21] != (a1[37] == 0)
      , (a1[20] ^ 0x4E | 0x50) == a1[15]
      , (a1[20] ^ 0x4E | 0x50) == a1[30]
      ,  a1[11] ==  a1[22] - 4
      ,  a1[22] == ( (a1[9] ^ a1[12]) & 0xB00B ^ 0x68)
      , a1[30] == a1[15]
      ,  a1[24] == ((( a1[5] - 456630272) & 0x9594815B | 0x4A6B0680) ^ 0xCEEB06B2)
      , a1[9] == 111
      , a1[4] == 67
      ,  a1[38] == (~ a1[13] &  a1[20] ^ 9 | 0x68)
      ,  (a1[13] ^ 0x62) == ( a1[20] & 0x4696)]

solver.add(constraints)

# Check for a solution
if solver.check() == sat:
    model = solver.model()
    solution = [model[a1[i]].as_long() for i in range(42)]
    print("".join(chr(x) for x in solution))
else:
    print("No solution found")
```

![image](https://github.com/IC3lemon/z3-summer-training/assets/150153966/bbc6572b-69a4-4e17-9ba2-506ded969cfd)
