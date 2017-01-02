# 80006D54 - ssbResetHeap
* RAM Routine Location : `0x80006D54`

Resets the "heap structure" pointed to in a0

### Inputs
* a0 : u32 Pointer to Heap Structure
* a1 : u32 Debug ID?
* a2 : u32 start of new heap
* a3 : u32 size of new heap

### Outputs
void

### Full Routine
```
  addiu t6, a2, a3
  sw    a1, 0x0000(a0)
  sw    a2, 0x000C(a0)
  sw    a2, 0x0004(a0)
  jr    ra
  sw    t6, 0x0008(a0)
```
