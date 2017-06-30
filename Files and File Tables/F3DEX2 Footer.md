# F3DEX2 Image Footer

I think this is the struct that gets passed to a function to draw a display list...
Look into routine at `0x8000F120` -> calls a baser function at `0x800092D0`

## Example Data
This is the footer for the Mario Series Logo in resource-0035.bin at +0x0990.
This is used on the Results screen.

It seems to be three footer structs back-to-back.

```
00000000 00000000 435B0006 44214005
00000000 00000000 00000000 00000000
3F800000 3F800000 3F800000 00000001
028601E0 00000000 00000000 00000000
00000000 00000000 00000000 3F800000
3F800000 3F800000 00000012 00000000
00000000 00000000 00000000 00000000
00000000 00000000 00000000 00000000
```

In RAM on results screen at `0x801728D0`:
80171F40
```
00000000 00000000 435B0006 44214005
00000000 00000000 00000000 00000000
3F800000 3F800000 3F800000 00000001
801726C0 00000000 00000000 00000000
00000000 00000000 00000000 3F800000
3F800000 3F800000 00000012 00000000
00000000 00000000 00000000 00000000
00000000 00000000 00000000 00000000
```

## Structure
Size = 0x2C

| Offset  | Type  | Name |
|---------|:------|------|
| + 0x00  | bitflag? | checks for 0x0012, 0x8000, 0x4000, 0x2000, 0x1000 |
| + 0x02  | u16   | Bank? |
| + 0x04  | u32   | Pointer to display list commands |
| + 0x08  | f32   | X Position? |
| + 0x0C  | f32   | Y Position? |
| + 0x10  | f32   | Z Position? |
| + 0x14  | f32   | Rotate around X axis |
| + 0x18  | f32   | Rotate around Y axis |
| + 0x1C  | f32   | Rotate around Z axis |
| + 0x20  | f32   | X Scale |
| + 0x24  | f32   | Y Scale |
| + 0x28  | f32   | Z Scale |
