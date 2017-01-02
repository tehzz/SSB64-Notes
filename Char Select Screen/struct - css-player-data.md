## Player Data Structure on CSS
Size: 0xBC (188) bytes

The set of 4 of these structures hold all active and used information about the player state on the CSS.

### Memory Location
An array of 4 structs at:

| Player  | Base Addr   |
|---------|-------------|
| 0x0     | 0x8013BA88  |
| 0x1     | 0x8013BB44  |
| 0x2     | 0x8013BC00  |
| 0x3     | 0x8013BCBC  |

### Fields
| Base Offset | Type  | Field           |
|-------------|------:|---------------|
| + 0x0       | u32   | Pointer I (??)  |
| + 0x4       | u32   | Pointer II (Cursor Info ?) |
| + 0x18      | u32   | Pointer for Panel Image Info ? |
| + 0x38      | u32   | CPU Level       |
| + 0x3C      | u32   | Handicap        |
| + 0x40      | u32   | Team            |
| + 0x48      | u32   | Character Index |
| + 0x4C      | u32   | Color           |
| + 0x50      | u32   | Shade           |
| + 0x54      | u32   | Cursor State    |
| + 0x58      | bool32| Character Selected? |
| + 0x5C      | u32?  | Unknown, but read often |
| + 0x60      | u32   | Select Frame + 0x1E |
| + 0x64      | f32   | ??? Token Pickup Movement |
| + 0x68      | f32   | ??? Token Pickup Movement |
| + 0x6C      | f32   | ??? Token Pickup Movement |
| + 0x70      | f32   | ??? Token Pickup Movement |
| + 0x74      | f32   | ??? Token Pickup Movement |
| + 0x78      | u32   | Frame Count for Token Pickup Movement |
| + 0x80      | u32   | Player Index of Held Token |
| + 0x84      | u32   | Player Panel State |
| + 0x88      | bool32| Character Selected II? |

#### Field Explanation
* Team: <0, 1, 2> <Red; Blue; Green>
* Character Index: This field holds the character index whenever a character is rendered in the player box at the bottom third of the screen. It makes no distinction between hovering over or selected.
* Cursor State: <0, 1, 2> <Pointing; Holding Token; Open Hand>
* Selected Frame: When a character is selected, the game loads the frame count on the CSS [`8013BDCC`] and adds 0x1E. Seems to prevent picking up token within 30 frames. Reads when picking up token, and if current frame < stored value, skip routine
* Token Pickup Movement: These 5 float values seem to have something to do with the path the player token takes when it's "recalled"  by pressing 'B'.
* Player Index of Held Token: Either the player index of currently held token, or `0xFFFFFFFF`.
* Player Panel State: <0, 1, 2> = < MAN, CPU, Closed >
