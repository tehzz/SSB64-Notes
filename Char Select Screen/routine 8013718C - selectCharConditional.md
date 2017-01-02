## Routine 8013718C - selectCharConditional
This routine will check if a player is able to select a character (token over a character portrait and no currently selected player for that token). If so, select a character, set up all memory states, and return `TRUE` (0x1). Else, return `FALSE` (0x0).

### Input Parameters
a0 : u32 playerPointer (? First Pointer from css-player-data struct)
a1 : u8  playerIndex
a2 : u8  heldToken
a3 : u8  charColor

### Output Parameters
v0 : bool character selected by routine
