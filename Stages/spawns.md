# Spawn Points Basic Info
There is an array of spawns in the stage main info file. There are pointed to by the
fifth and final pointer of the collision and spawn pointer set, which is pointed at by the 
pointer at (+0x40 / +0x54) in the main stage file.

## Structure
```
typedef struct {
  id: u16 SpawnID,
  x : i16,
  y : i16
} Spawn

sizeof Spawn == 0x06 bytes
```

## SpawnID Enums
| Value | Type  |
|-------|-------|
| 0x00  | P1 Starting Spawn |
| 0x01  | P2 Starting Spawn |
| 0x02  | P3 Starting Spawn |
| 0x03  | P4 Starting Spawn |
| 0x04  | Item Spawn |
| 0x0D  | Tornado Spawn (Hyrule) ?|
| 0x13  | PC Bumper Location ?|
| 0x15  |
| 0x18  |
| 0x19  |
| 0x1A  |
| 0x1B  |
| 0x1C  |
| 0x1D  |
| 0x1E  |
| 0x1F  |
| 0x20  | Re-spawn platform default |
| 0x21  | 1P Player Spawn |
| 0x22  | 1P Ally 1 Spawn |
| 0x25  | 1P Mode CPU Spawn |
| 0x26  | 1P Mode CPU Spawn 2 |
| 0x27  | 1P Mode CPU Spawn 3 |
| 0x2B  | 1P Mode New CPU Spawn |
| 0x2C  |
| 0x2D  |

* **Item Spawn**: index of all 0x04 types are moved into a byte array
