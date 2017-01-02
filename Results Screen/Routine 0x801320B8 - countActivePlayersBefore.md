## Analysis of Routine `0x801320B8`
countActivePlayersBefore

This counts the number of active, in-game players before player a0. "Before" refers to
the actual player index/number. So, player 1 is before player 2, who is before player 3, etc.


Eg, P2 and P4 in game
| player | input | output|
|--------|-------|-------|
| P2     | 0x1   | 0x0   |
| P4     | 0x3   | 0x1   |

### Routine Input(s)
* a0 : u8 player index

### Routine Output(s)
* v0 : u32 number of players before index in a0
