# 80004950 - ssbResetMainHeap
* RAM Routine Location : `0x80004950`

This routine resets the "main heap" that callML:alloc uses. The routine itself is another wrapper for a more general reset routine (`80006D54`). It moves a0, a1 to a2, a3, and sets a0 to `0x800465E8` and a1 to "0x10000". Persumably, a1 is a debug ID.

### Inputs
* a0 : u32 start of new heap
* a1 : u32 size of new heap

### Outputs
void
