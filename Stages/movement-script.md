# Stage Object Movement Script Documentation
There is a simple scripting language to move platforms, etc. in SSB64's stages

## Command Structure
bitfield struct (u32, but important parts seem to be only in upper half...)
* 31:25 (7 bits): Command
  * Checks for < 0x12
  * Used as input to jump table at `0x8003DDEC`, but the called function can also
  use the command value to branch within itself
* 24:15 (10 bits): Property to Modify
  * If equal to 0, break?
  * Otherwise, loop over each bit
    * find the first set bit
    * Offset displaylist pointer? ('0x80043B60')
    * bit
    * Move things
    * Does this say what to do with what follows?
      * bit 0: X Axis Rotation
      * bit 1: Y Axis Rotation
      * bit 2: Z Axis Rotation
      * bit 3:
      * bit 4: X Translate
      * bit 5: Y Translate
      * bit 6: Z Translate
      * bit 7: X Scale
      * bit 8: Y Scale
      * bit 9: Z Scale
* 15:0 (15 bits)
  * Custom value (I've only seen frame times here, if used)
  * if value < 0, add 4f800000f32 (4.29497e+09)

## Commands
These are shifted left 25

| Value | Value `<< 0x19` | Routine   | Command |
|-------|-----------------|-----------|---------|
| 0x00  | 0x00xxxxxx      | 8000C7E0  |         |
| 0x01  | 0x02xxxxxx      | 8000C6CC  |         |
| 0x02  | 0x04xxxxxx      | 8000C548  |         |
| 0x03  | 0x06xxxxxx      | 8000C2B0  | timed move to 1?  |
| 0x04  | 0x08xxxxxx      | 8000C2B0  | immediate move to 1? |
| 0x05  | 0x0Axxxxxx      | 8000C3B8  |  |
| 0x06  | 0x0Cxxxxxx      | 8000C3B8  |  |
| 0x07  | 0x0Exxxxxx      | 8000C4D0  |  |
| 0x08  | 0x10xxxxxx      | 8000C1AC  | move to 2?|
| 0x09  | 0x12xxxxxx      | 8000C1AC  | immediate move to 2? |
| 0x0A  | 0x14xxxxxx      | 8000C584  | pause then immediate move?|
| 0x0B  | 0x16xxxxxx      | 8000C584  |  |
| 0x0C  | 0x18xxxxxx      | 8000C70C  |  |
| 0x0D  | 0x1Axxxxxx      | 8000C798  |  |
| 0x0E  | 0x1Cxxxxxx      | 8000C674  | goto ?  |
| 0x0F  | 0x1Exxxxxx      | 8000C868  |  |
| 0x10  | 0x20xxxxxx      | 8000C8B4  |  |
| 0x11  | 0x22xxxxxx      | 8000C944  |  |

## Utils
```javascript
function separate(hex) {
  const props = [
    {bit: 0, val: "X-Axis Rotation"},
    {bit: 1, val: "Y-Axis Rotation"},
    {bit: 2, val: "Z-Axis Rotation"},
    {bit: 3, val: "Unknown bit 3"},
    {bit: 4, val: "X-Axis Translate"},
    {bit: 5, val: "Y-Axis Translate"},
    {bit: 6, val: "Z-Axis Translate"},
    {bit: 7, val: "X-Axis Scale"},
    {bit: 8, val: "Y-Axis Scale"},
    {bit: 9, val: "Z-Axis Scale"}
  ];

  let cmd  = (hex & 0xFE000000) >>> 0x19;
  let bank = (hex & 0x01FF8000) >> 0xf;
  let custom = (hex & 0x7FFF);

  let properties = props
    .filter(prop => bank & (1 << prop.bit))
    .map(prop => prop.val);

  console.log(`  Command: 0x${cmd.toString(16)}
  Property: ${properties.join(", ")}
  Bits: 0b${bank.toString(2)}
  Custom: ${custom}`)
}
```
