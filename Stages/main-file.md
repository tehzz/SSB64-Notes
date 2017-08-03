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
| 0x44    | 0x58    | u8?     |
| 0x48    | 0x5C    | req_ptr | Pointer to ssb image footer for background image |
| 0x4C    | 0x60    | rgba8888| Magnifying Glass Background Color (no alpha) |
| 0x50    | 0x64    | rgba8888| P1 Logo Color....? (alpha doesn't matter)|
| 0x54    | 0x68    | rgba8888| P2 Logo Color....? (alpha + blue doesn't matter)|
| 0x58    | 0x6C    | rgba8888| P3 Logo Color....?|
| 0x5C    | 0x70    | rgba8888| P3 Logo Color....?|
| 0x60    | 0x74    | f32     | Lighting? X? |
| 0x64    | 0x78    | f32     | Lighting? Y? |
| 0x68    | 0x7C    | f32     | Camera X Rotation (around x axis) |
| 0x6C    | 0x80    | i16     | +Y Camera Limit? (won't move frame past this y) |
| 0x6E    | 0x82    | i16     | -Y Camera Limit? |
| 0x70    | 0x84    | i16     | +X Camera Limit? |
| 0x72    | 0x86    | i16     | -X Camera Limit? |
| 0x74    | 0x88    | i16     | +Y Blastzone |
| 0x76    | 0x8A    | i16     | -Y Blastzone |
| 0x78    | 0x8C    | i16     | +X Blastzone |
| 0x7A    | 0x8E    | i16     | -X Blastzone |
| 0x7C    | 0x90    | u32     | Stage Background Music Index |
| 0x80    | 0x94    | u32     | 0x00 pad? |
| 0x84    | 0x98    | res_ptr | Pointer to beginning of file? |
| 0x88    | 0x9C    | i16     | Falling Whistle Y Threshold |
| 0x8A    | 0x9E    | i16     | 1P +Y Camera Limit |
| 0x8C    | 0xA0    | i16     | 1P -Y Camera Limit |
| 0x8E    | 0xA2    | i16     | 1P +X Camera Limit |
| 0x90    | 0xA4    | i16     | 1P -X Camera Limit |
| 0x92    | 0xA6    | i16     | +Y Blastzone |
| 0x94    | 0xA8    | i16     | -Y Blastzone |
| 0x96    | 0xAA    | i16     | +X Blastzone |
| 0x98    | 0xAC    | i16     | -X Blastzone |



* **0x44**: Byte/bool that influences loading of from 0x8012E800 (looks like function pointers)
* **Falling Whistle Y Threshold**: When a character's previous frame height > val > current height?

## Stage Specific Additional information
### 0x04 Hyrule (file 265)
Files can have information starting at 0xBC that seems to be stage specific. Hyrule
has the tornado uses a set of 6 or 7 words after:

| Offset from 0xBC | type | value |use |
|------------------|------|-------|----|
| + 0x0            | u32  |  0x02 |
| + 0x4            | i32  |  0x0E | Tornado Damage |
| + 0x8            | i32  |  0x5A | Launch angle (deg); 0 = horizontal direction of input? |
| + 0xC            | i32  |  0x3C | Tornado Base Knockback |
| + 0x10           | i32  |  0x00 | When >0, use as fixed knockback? Else calculate kb |
| + 0x14           | i32  |  0x73 | Tornado Knockback Growth? |
| + 0x18           | i32  |  0x00 | ?? |
