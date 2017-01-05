# 80134674 - colorInUse
Check if a given color for a given character is already selected. Return TRUE if that is the case, else return false

## Parameters
### Inputs
* a0 : u32 character
* a1 : u32 player-index
* a2 : u32 color-index

### Output
* v0 : bool color-already-in-use

### Code Summary
Compare each player's character to character `a0`. If the same, comparable that player's character's color with color `a2`. If there's a match, return TRUE; else, return FALSE.

### Full Routine
