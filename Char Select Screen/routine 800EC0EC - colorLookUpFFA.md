# 800EC0EC - colorLookUpFFA
This looks up the free-for-all "C-Button" color for a character from an array.

## Parameters
### Inputs
* a0 : u32 character
* a1 : u32 C-Button Direction (or, "i")

### Output
* v0 : u32 color index

### Code Summary
The internal array at `0x8012B830` has 4 bytes entries for both FFA and Team Mode. Something like:
 ```
 u8 MarioFFA[4] = {00, 01, 02, 03};
 u8 MarioTM[4] = {00, 03, 04, 04};
 ...

ColorLUT[12] = {[MarioFFA, MarioTM], [FoxFFA, FoxTM], ...};
```

This routine can easily pull out the FFA colors for a given C-Button (Up = 0, Right = 1, Down = 2, Left = 3)

### Full Routine
```
800EC0EC  sll   t6, a0, 0x3
          addu  t7, t6, a1
          lui   v0, 0x8013
          addu  v0, v0, t7
          jr    ra
          lbu   v0, 0xB830(v0)
```
