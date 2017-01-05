## Routine 8013718C - selectCharConditional
This routine will check if a player is able to select a character (token over a character portrait and no currently selected player for that token). If so, select a character, set up all memory states, and return `TRUE` (0x1). Else, return `FALSE` (0x0).

## Parameters
### Input
* a0 : u32 playerPointer (? First Pointer from css-player-data struct)
* a1 : u8  playerIndex
* a2 : u8  heldToken
* a3 : u8  charColor

### Output
* v0 : bool characterSelectedByRoutine?

## Code Flow
* Generate pointer to `css-player-data` struct for player `a1`
* Check the cursor state of player `a1` (base + 0x54)
  * If ( state != 0x1 ( Holding_Token ) )
  * return FALSE
  * ELSE:
* Get the player index of the held token (base + 0x80), and use that to calculate a new pointer to `css-player-data` for that player
* Load that player's character (base + 0x48), and compare to Not_Selected (0x1C)
  * If character == not selected:
    * play error sound ( ssb.playFGM(0x00A5) )
    * return FALSE
  * ELSE:
* Call css.selectCharacter( playerIndex, charColor ) [`80131C74`]
* Load the current frame number, add 0x1E (30 frames), and store at `base + 0x60` to lock out picking up the token with A for a half second.
* Return TRUE
