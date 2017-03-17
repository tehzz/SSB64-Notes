## Analysis of Routine `0x80133718`

countPlayersPlacedAbove

This seems to count the number of players who placed above the player index passed in a0.
E.g., if p1 1st, p2 3rd, p3 1st, p4 4th, then this would return:
* P1 -> 0
* P2 -> 2
* P3 -> 0
* P4 -> 3

### Routine Inputs
* a0 : u32 player index

### Outputs
* v0 : u32 number of players who placed higher than player in a0 

### Stack Map
Doesn't use the stack

### Routine
Accumulates something in v1

Seems to check for each player that not including player in `a0`.  

For each player (not including player `a0`), it:
1) Checks if player N is in game
2) Gets the place for player N and player `a0`
3) If (N.place < a0.place) then nothing
   Else (eg, N.Place >= a0.place) add 1 to v1
