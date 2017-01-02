# 80006CEC - ml : alloc
* RAM Routine Location: `80006CEC`

This seems to be SSB64's version of malloc. It's not really comparable at all to C's malloc, but it's what Sakurai and co. put together. This routine seems to be mainly called by `80004980` (callML.alloc), which wraps the call to ml:alloc with a constant pointer of `0x800465E8`. But, it is called directly sometimes.

### Inputs
* a0 :  u32 pointer to some memory structure
* a1 :  u32 requested size (in bytes?)
* a2 :  u32 byte alignment?

### Outputs
* v0 :  u32 pointer to memory of requested size

Errors with string at `8003D910` ("ml : alloc overflow #%d") if the request size + current heap location >= top of heap (+0x8)

#### Memory Struct
| Offset | Type | Field |
|--------|------|-------|
| + 0x0  | u32  | An ID of some sort (used by debug error string) |
| + 0x4  | u32  | Initial floor of "heap" |
| + 0x8  | u32  | Top of "heap" |
| + 0xC  | u32  | Current heap location |
