## Analysis of Routine `0x80131B90`
### saveBattleStats

This routine runs right after a battle finishes, and it saves the KOs/TKOs/Percent Dealt/Percent Recieved to a universal structure that is always in RAM [`0x800A44E0`?]. This is used when looking at the "VS Mode Data" in the Data Section.

### Calling
This routine is called by the "initial main"/"initializor" function [`0x80138B70`] on the results screen. This is only called when the battle was not "No Cotentest"-ed.

### Routine Inputs
void
### Routine Outputs
void

### Stack Map
size: 0x58 bytes
sp + 0x3C  <- ra
sp + [0x1C to 0x38] <- initial s0 through s8

sp + 0x40 <- save pointer to current player struct from v0

### Saved Register Map
s0 : pointer to character saved data struct (&saved-data -> {character})
s1 : inner loop i / inner player index
s2 : outer loop i / (outer) player index
s3 : 800A4D08 -> stage struct/ player structs
s4 : character index (variable based on loop number)
s5 : 0x2  (Player Status enum; Not-In-Game)
s6 : 0x270F (??)
s7 : 0x4; i < 4
s8 : player struct size (0x74)

v0 : in loop, pointer to a given player struct (player[i])
### Code Overview

### Code Flow
* 80131B90
  * Set up stack
  * Load two immediate pointers: (a0 <- `800A44E0`) and (s3 <- `800A4D08`)
  * Load the active stage address byte from s3 + 1 [`0x800A4D09`]
  * Load two half words and one byte from a0 (800A4ABC, 800A4ABE, 800A4AC0)
  * Use the stage index to sll 0x1 [0x1 << stage-index]
  * OR that value with the returned half-word from `0x800A4ABC` (for DL, it was 0x40)
    * Store modified value back into RAM
    * Stage Played Flags...?
  * Incremement half-word at `0x800A4AC0` and store back into RAM
  * Check if the byte from `0x800A4ABE` is < 0x00FF (slti 0x00FF, byte; beqz)
    * If so, skip a few instructions?
      * They add one to `0x800A4ABE` byte, and store back into RAM
      * This increments the byte, but doesn't let it go over 0xFF
  * Set up saved registers with looping constants
* 80131C20; Start of Loop Body?
  * Check if the player is in game
  * Load word from 800A4D08 + 0x18 (frame number?)
    * Divide by 0x3C (60) -> calculate seconds?
  * load character address into s4
    * multiply by 0x5C (92)
    * add to pointer in a0 (start of Saved-Data struct)
    * store new pointer in s0
  * 80131C54; Total Time
    * Load word from Saved-Data.char base + 0x18 (time counter?)
    * Add to division result (now total seconds)
    * Check if Total Time < 3,600,000 s (1,000 hours)
      * If over, ignore total and store 0x36EE7F (999h59m59s)
      * else, store new total time at base + 0x18
  * 80131C80; Total Damage Dealt
    * Load word from SavedData.char base + 0x1C
    * Load total damage dealt for character
    * Add previous two words together
    * Check if sum < 1,000,000
      * If not, store 999,999 insted of sum
  * 80131CAC; Total Damage Received
    * same as total damage dealt, but base + 0x20
  * 80131CD4; ???? SD?
    * SavedData.char + 0x26 and Player + 0x30 ???
        * (Player + 0x50 in routine due to pointer shenanigans)
    * Check if & 0xFFFF is less than 0x2710 (10,000)
  * 80131CF0
    * Incremement half-word at SavedData.char + 0x28
  * 80131CFC
    * Call ???? [`0x80131B78`]
    * Add return to u16 SavedData.char + 0x2A and store
  * 80131D18; begin inner loop
    * now, there is an outter i (player) and an inner i (player) index
    * calculate:
      * inner_player * 0x74 for Player Struc
      * outer_player * 0x74 + 800A4D08
        *[outer_player *8 - 1ip * 4 + 1ip * 4]
      * outer_player.character * 46 + 800A44E0
        * [character * 4 - 1c * 8 - 1c *2]
    * get inner_player ingame status, and check if in game
      * if not in-game, continue to next loop iteration
    * get inner_player.character
      * ip.char * 2 + (outer_player.character * 46 + 800A44E0)
    * calc inner_player * 4 + 800A4D08 (t6 <-)

### Loop Peudo-code:
  ```
  for(i = 0; i < 4; i++) {
    if (player[i].status == NOT-IN-GAME) continue

    var totalTimeUsed = player[i].totalTime + battleFrames/60
    player[i].totalTime = totalTimeUsed < 0x36EE80 ? totalTimeUsed : 0x36EE7F

    var totalDamage = player[i].totalDamage + player.damageDealt
    player[i].totalDamage = totalDamage < 0xF4230 ? totalDamage : 0xF423F

    var totalReceived = player[i].totalReceived + player.damageReceived
    player[i].totalReceived = totalReceived < 0xF4230 ? totalReceived : 0xF423F
  }
  ```
