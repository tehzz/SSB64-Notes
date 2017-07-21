# Main Stage File Documentation
Hopefully this will document every offset in the main stage file.

In general, the main stage file points to structure in other, req'd files. But,
it does contain some data beyond pointers

## Type 0x14 or 0x00
Some main stage files start with 0x14 bytes of additional information. They seem
to be involved in item generation? Map eventually.

## Fields
* The "**res_ptr**" type is two u16 values. The first value (upper half of the u32) is the offset in words to the
next "res_ptr", and the second value (lower half of the u32) is the offset in the words for the pointer
* A "**req_ptr**" is the same as a `res_ptr`, but it points to a location in another file

| Offset  | + 0x14  | Type    | Description  |
|---------|---------|---------|--------------|
| 0x00    | 0x14    | req_ptr | Point to main stage display list |
| 0x40    | 0x54    | req_ptr | Point to stage collision and spawn structure |
| 0x48    | 0x5C    | req_ptr | Pointer to ssb image footer for background image |
| 0x6C    | 0x80    | i16     | +Y Camera Limit? |
| 0x6E    | 0x82    | i16     | -Y Camera Limit? |
| 0x70    | 0x84    | i16     | +X Camera Limit? |
| 0x72    | 0x86    | i16     | -X Camera Limit? |
| 0x74    | 0x88    | i16     | +Y Blastzone |
| 0x76    | 0x8A    | i16     | -Y Blastzone |
| 0x78    | 0x8C    | i16     | +X Blastzone |
| 0x7A    | 0x8E    | i16     | -X Blastzone |
| 0x7C    | 0x90    | u32     | Stage Background Music Index |
