# Battlefield Collision and Spawns
In File `0x74` (116) with pointers starting at `+0x4388`

## Internal File Pointers for Collisions
Raw from resource file:
```
00010000 10E4107D 10E510A1 10E610B3
10E810C2 00120000 FFFF10C7 00000000
00000000 00000000
```

Resource File Offsets converted (`u32 & 0x000FFFF << 2`):
```
00010000 000041F4 00004284 000042CC
00004308 00120000 0000431C 00000000
00000000 00000000
```

| Offset  | Type  | Value     | Description |
|---------|-------|-----------|-------------|
| +0x00   | u16   | 0x0001    | Collision on/off? |
| +0x02   | u16   | 0x0000    | pad? |
| +0x04   | u32   | 000041F4  | Pointer to start of collision point array |
| +0x08   | u32   | 00004284  | Pointer to collision connection list |
| +0x0C   | u32   | 000042CC  | Pointer to collision connection info |
| +0x10   | u32   | 00004308  | Pointer to unknown list... |
| +0x14   | u16   | 0x0012    | Total number of spawn points |
| +0x16   | u16   | ----      | pad |
| +0x18   | u32   | 0000431C  | Pointer to spawn points array |
| >= 0x20 | --    | ----      | Are these used? Or just pad to align (0x10)? |

* Collision Point Array (+0x4) :
  * `[ (x: i16, y: i16, flags: u16 ) ]`
* Collision Connection List (+0x8) :
  * `[ collisionPointIndex: u16 ]`
* Connection Info (+0xC) :
  * `[ offset_colConectionList: u16, length: u16 ]`
  * starting at `offset` in the connection list, connect the next `length - 1` collision points
* Unknown Array (+0x10) :
  * Seems to have something to do with top / bottom / side collision?
* Spawn Points Array (+0x18):
  * `[ (type: u16, x: i16, y: i16) ]`

## Collision Info
### Collision Point Array
Points are only 2D. If the 'y' value is changed too much from its original value,
the collision no longer stops falling characters.

| Index | X Val | Y Val | Flag | Description |
|-------|-------|-------|------|-------------|
| 0     | -645  | 1021  | 4000 | left plat, right edge |
| 1     | -940  | 1021  | 4000 | left plat, middle-right |
| 2     | -1465 | 1021  | 4000 | left plat, middle-left |
| 3     | -1695 | 1021  | 0000 | left plat, left edge |
| 4     | 1725  | 1020  | 4000 | right plat, right edge  |
| 5     | 1593  | 1020  | 4000 | right plat, middle-right |
| 6     | 860   | 1020  | 4000 | right plat, middle-left |
| 7     | 675   | 1020  | 0000 | right plat, left edge |
| 8     | -525  | 1801  | 4000 | top plat, left edge |
| 9     | -364  | 1801  | 4000 | top plat, middle-left |
| 10    | 160   | 1801  | 4000 | top plat, middle-right |
| 11    | 525   | 1801  | 0000 | top plat, right-edge |
| 12    | -1387 | -270  | 0000 |    |
| 13    | -1803 | -270  | 0000 |    |
| 14    | -1950 | 0     | 8000 | main plat, left edge  |
| 15    | 1950  | 0     | 0000 | main plat, right edge |
| 16    | 1803  | -270  | 0000 |    |
| 17    | 1387  | -270  | 0000 |    |
| 18    | 1246  | -607  | 0000 |    |
| 19    | 623   | -607  | 0000 |    |
| 20    | 525   | -1080 | 0000 | Lower Rect, bottom right   |
| 21    | -525  | -1080 | 0000 | Lower Rect, bottom left   |
| 22    | -623  | -607  | 0000 |    |
| 23    | -1246 | -607  | 0000 |    |

* Flags
  * Upper Half is flag
    * 0x80 - ledge is grab-able
    * 0x40 - plane is drop-through-able
  * Lower Half has two functions: as an index into a friction table and as a case
  for a jump tbale
  * `idx << 2 + 0x8012C4E0` for friction
  * `idx - 7 && idx - 7 < 9` for jumptable
    * 0x00 : Normal (?)
    * 0x07 : Lava Sideways 1%
    * 0x08 : Acid Up Large 10% (Planet Zebes)
    * 0x09 : Lava Up Small 10% (DK / Mario BtP?)
    * 0x0A : Spikes (?) Sideways 10%
    * 0x0B : Lava Up Small 1%
    * 0x0C : Nothing
    * 0x0D : Nothing
    * 0x0E : Nothing (BtP Platform?)
    * 0x0F : Lava Up 1%
  * Values >= 0x10 seem to be ice, but it looks like this index is just overflowing
    * 0x10 : Ice (Super Slippery)
    * 0x11 : Ice (Less Slippery)
  * Lower half special effects determined at ~0x800E5C60
    * jump table at 8012FE64

| Index | Float |
|-------|-------|


### Collision Connection List
This is an "ordered" list of collision points (as u16 indices into the above array).
Together with the next table, this defines a collision plane. Planes seem to be
defined in any order

```
        0x00      0x04      0x08      0x0C
        ---------------------------------------
0x00:   0008 0009 000A 000B 0000 0001 0002 0003
0x10:   0004 0005 0006 0007 000E 000F 0014 0015
0x20:   0012 0013 0016 0017 0010 0011 000C 000D
0x30:   000F 0010 0011 0012 0013 0014 000D 000E
0x40:   0017 000C 0015 0016
```

### Collision Connection Info
This sets the actual collision planes by giving a u16 offset into the
connection list and a u16 length

| Index | List Offset | Length  | Points        | Feature   | Plane |
|-------|-------------|---------|---------------|-----------|-------|
| 0     | + 0x00      | 0x4     | 8, 9, 10, 11  | Top Plat  | (-525, 1801), (525, 1801) |
| 1     | + 0x08      | 0x4     | 0, 1, 2, 3    | Left Plat | (-645, 1021), (-1695, 1021) |
| 2     | + 0x10      | 0x4     | 4, 5, 6, 7    | Right Plat| (1725, 1020), (675, 1020) |
| 3     | + 0x18      | 0x2     | 14, 15        | Main Plat | (-1950, 0), (1950, 0) |
| 4     | + 0x1C      | 0x2     | 20, 21        | Lower Rect Bottom | (525, -1080), (-525, -1080) |
| 5     | + 0x20      | 0x2     | 18, 19        | ? | (1246, -607), (623, -607) |
| 6     | + 0x24      | 0x2     | 22, 23        | ? | (-623, -607), (-1246, -607) |
| 7     | + 0x28      | 0x2     | 16, 17        | ? | (1803, -270), (1387, -270) |
| 8     | + 0x2C      | 0x2     | 12, 13        | ? | (-1387, -270), (-1803, -270) |
| 9     | + 0x30      | 0x2     | 15, 16        | right upper wall | (-1950, 0), (1803, -270) |
| 10    | + 0x34      | 0x2     | 17, 18        | ? | (1387, -270), (1246, -607) |
| 11    | + 0x38      | 0x2     | 19, 20        | ? | (623, -607), (525, -1080) |
| 12    | + 0x3C      | 0x2     | 13, 14        | left upper wall | (-1803, -207), (-1950, 0) |
| 13    | + 0x40      | 0x2     | 23, 12        | ? | (-1246, -607), (-1387, -207) |
| 14    | + 0x44      | 0x2     | 21, 22        | ? | (-525, -1080), (-623, -607) |


Raw:
```
 0: 0000 0004
 1: 0004 0004
 2: 0008 0004
 3: 000C 0002
 4: 000E 0002
 5: 0010 0002
 6: 0012 0002
 7: 0014 0002
 8: 0016 0002
 9: 0018 0002
10: 001A 0002
11: 001C 0002
12: 001E 0002
13: 0020 0002
14: 0022 0002
```

### Unknown Array
First half = unknown...
then (u16 startIdx, u16 length) <- similar to melee
top, bottom, right, left

```
0001 0000  0004 0004
0005 0009  0003 000C
0003 0000
```
