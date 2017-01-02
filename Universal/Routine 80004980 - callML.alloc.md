# 80006CEC - call ml:alloc
* RAM Routine Location : `80006CEC`

Wraps a call to ml : alloc [`80006CEC`] with a constant a0 value of `0x800465E8`.

### Inputs
* a0 : u32 size of request
* a1 : u32 byte alignment?

### Output
* v0 : pointer to RAM on heap
