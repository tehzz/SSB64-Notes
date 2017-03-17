## Analysis of Routine `0x80133810`

getModifiedPlayerPlace

Modifidy a players place by the number of players in front of and tied with the player in `a0`.

Possible Return values
0x0 (1st by self) to 0x4 (last by self)?

### Routine Inputs
* a0 : player index

### Outputs
* v0 : Sum(Place[a0], ArrayI[PlacedHigher], ArrayII[PlacedSame])
  * ArrayI[PlacedHigher] = PlacedHigher > 2 ? 1 : 0
  * ArrayII[PlacedSame]  = PlacedSame > 2 ? 1 : 0

### Stack Map
size: 0x50 bytes
sp + 0x50 <- a0 [player index]
sp + 0x14 <- ra
sp + 0x40 <- array at 0x80139188; 4 memeber uint32
sp + 0x2C <- array at 0x80139198; 5 memeber unit32
sp + 0x24 <- count of players who placed higher than player in `a0`
sp + 0x20 <- &player[a0].place (not the place value itself)

### Arrays
#### Array 1 : Count of Players Placed Above Player a0
In RAM at `0x80139188` on Results Screen
| Number    | value [unit32] |
|-----------|----------------|
| 0 players | 0x0            |
| 1 player  | 0x0            |
| 2 players | 0x1            |
| 3 players | 0x1            |

#### Array 2 : Sum of Players at Same Place as Player a0
In RAM at `0x80139198` on Results Screen
| Number    | value [unit32] |
|-----------|----------------|
| 0 players | 0x0 [impossible?]|
| 1 player  | 0x0            |
| 2 players | 0x1            |
| 3 players | 0x1            |
| 4 players | 0x1            |

### Code Flow
* Addresss
  * puts a 4 memeber array onto the stack from `0x80139188`
* 80133850
  * puts ?? memeber array onto stack from `0x80139198`
* 8013387C
  * Calls routine "countPlayersPlacedAbove" [`80133718`] with player index in a0. The returned count is saved on the stack at sp + 0x24
* 80133888
  * Get the place for player `a0`, and store the pointer to that place value at 0x20
* 801338A0
  * Call "sumPlayersAtPlace" [`80133684`] for player a0's place
* 801338A8
  * Use number of players at the same place as player a0 to offset 5 member array II (at sp + 0x2C) by doing:
    * t8 <- (v0 << 2) + sp + 0x28
  * Find the difference between the player a0's place and the number of players who placed higher than him by
    * pPlace = *(sp+0x20) [ value between <0 - 3> ]
    * countAbove = sp + 0x24
    * t5 <- (pPlace << 2) + -(countAbove << 2)
* 801338DC
  * Calculate the return value by adding:
    * v1 + t8 + t5
    * pPlace + Array 2 Value + Array 1 Value
