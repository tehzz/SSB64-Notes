# Structure Global Game Data
* Struct Size: 0x20 + (0x74 * 4) bytes
* Array Base: 0x800A4D08

This struct holds basic information on the game state (time mode?, team mode?, etc.)
and on state for each player (character selected, CPU level, etc.).

Due to this struct being compose of two structures (the game data and an array of 4 player data structs), any offset into the player data has to have an extra 0x20 bytes added. Be mindful of that extra padding when looking at dissassemblies.

## Array (Player) Offsets
| Player  | Index | Struct        | RAM Offset  |
|---------|-------|---------------|-------------|
| Game    | ---   | Game State    | 800A4D08    |
| P1      | 0x0   | Player State  | 800A4D28    |
| P2      | 0x1   | Player State  | 800A4D9C    |
| P3      | 0x2   | Player State  | 800A4E10    |
| P4      | 0x3   | Player State  | 800A4E84    |

## Game State Known Fields
* Size = `0x20 bytes`

| Base Offset | Type    | Field                   |
|-------------|--------:|-------------------------|
| + 0x0       |
| + 0x1       | u8      | Current Stage Index     |
| + 0x2       | u8 bool | Team Mode               |
| + 0x3       | u8      | Time / Stock BitFlag    |
| + 0x4       |
| + 0x5       |
| + 0x6       | u8      | Starting Time           |
| + 0x7       | u8      | Starting Stock - 1      |
| + 0x8       | u8      | Handicap Mode Enum      |
| + 0x9       | u8 bool | Team Attack On?         |
| + 0xA       | u8 bool | VS Select Stage?        |
| + 0xB       | u8      | Damage %                |
| + 0xC       | u32 flag| Item Flags              |
| + 0x10      |
| + 0x11      | u8 bool | Pause State?            |
| + 0x14      | u32     | Time Remaining (Frames) |
| + 0x18      | u32     | Frame Counter?          |
| + 0x1C      | u8      | Item Appearance % Enum  |


* Time / Stock BitFlag:
  * `b00000001` = Time On
  * `b00000010` = Stock On
* Handicap Mode Enum:
```
 enum {
   Off    = 0,
   Manual = 1,
   Auto   = 2,
 }
 ```
* Item Flags:
* Pause State: This seems to work in conjunction with the Item Appearance Enum (maybe training mode...?)
  * 0 = No Items Spawn
  * 1 = Items Spawn
  * 2 = Pause
  * 3 = Remove BG?
  * 8 = Inifinite Loop (recoverable, though)
* Item Appearance % Enum:
```
enum {
    None      = 0,
    Very Low  = 1,
    Low       = 2,
    Middle    = 3,
    High      = 4,
    Very High = 5
}
```

## Player State Known Fields
* Size = `0x74 bytes`

| Base Offset | Type  | Field                   |
|-------------|------:|-------------------------|
| + 0x0       | u8    | Player's CPU Level      |
| + 0x1       | u8    | Player's Handicap Level |
| + 0x2       | u8    | Player's In Game Enum   |
| + 0x3       | u8    | Player's Character Index|
| + 0x4       | u8    | Player's Team           |
| + 0x5       |       | ??????                  |
| + 0x6       | u8    | Player's Color          |
| + 0x58      | u32   | Pointer to Another Struct (used for camera?) |

* In Game Enum: `{ MAN = 0, CPU = 1, Not-in-Game = 2 }`
