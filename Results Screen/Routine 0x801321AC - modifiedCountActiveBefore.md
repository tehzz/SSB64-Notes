## Analysis of Routine `0x801321AC`
name? modifiedCountActiveBefore ?

This seems to work as a wrapper for countActivePlayersBefore, but checks for certain conditions

What are those conditions? Make a list!?
* 3 Players Total, and routine called with middle player
* 4 Players -??
* 2 Players = No Changes for two players

This seems to be used for getting a "position index" for where character models should be
placed left-to-right on the results screen

### Routine Input(s)
* a0 : u8 player index

### Routine Output(s)
* v0 : u32 modified countActivePlayersBefore value

### Stack Map
* size: 0x20 bytes
* sp + 0x14 <- ra
* sp + 0x20 <- a0 [player index]
* sp + 0x1C <- count of active players before player index a0; return from countActivePlayersBefore

### Code Flow
* 801321BC
  * Put ra and a0 onto stack
  * Call countActivePlayersBefore [`0x801320B8`] with the player index inputted in a0. Save return value on stack [sp + 0x1C]
  * Call countPlayerAtPlace [`0x80133684`] with first place [a0 <- `0x0`] to get the number of first place players
* 801321C8
  * Branch on the returned count value.
    * If not equal to one, exit routine [`0x80132338`] and return count of active players before player index a0 (ie, return from countActivePlayersBefore / sp+0x1C)
    * If equal, continue
* 801321DC
  * Call getTotalNumberOfPlayers [`0x8013205C`]. Use the returned number to branch
    * 2 Players : jump to exit [`0x80132338`]; return count of active players before player index a0
    * 3 Players : 0x801321FC
    * 4+ Players: 0x80132280
* 801321F8; 3 Players Branch
  * Get the place for player index a0
    * 0x80139BB0 + [(sp+0x20) << 2]
  * Branch on first place or not
    * First Place : 0x8013221C
    * Not First   : 0x80132214

  * 80132214; Not First Place Branch
    * Another call to countActivePlayersBefore [`0x801320B8`] for the routine's input player index
    * Branch on that value == 0x1?
      * If not equal, exit [`0x80132338`]; return count of active players before player index a0 (`lw   v1, 0x1C(sp)`)
      * If Equal:
        * Call an unknown routine [`0x80132100`] with zero in a0 (a0 <- 0x0).
        * Use the return from the unknown routine to call countActivePlayersBefore [`0x801320B8`]
        * Unconditional branch to Exit [`0x80132338`]; Put the return from the just called countActivePlayersBeforeUse into v1 (ie, return this value)

  * 8013221C; First Place Branch

* 80132280; 4 Players Branch

* 80132338; Exit
  * This moves the value from v1 to v0, and returns to the caller. v1 is typically set-up by one of the main branches beforehand.
