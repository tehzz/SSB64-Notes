# Results Screen
###### Character Based Arrays, Functions, and General Misc for the Results Screen

### RAM Space on Results Screen
* "Malloc" has an upper limit of `0x803903E0`
  * Standard routine after that address
* `0x01010101` pattern starts at `0x80392A00`

### Character Data/Model Loading
At `0x80138C58` / ROM `0x157DF8`:

```
80138C58    or    s0, s0, 0x0001
      5C    jal   0x800D786C
      60    or    a0, s0, r0
      64    addiu s0, s0, 0x0001
      68    slti  at, s0, 0x000C
      6C    bnez  at, 0x80138C5C
      70    nop
```

Routine `0x800D786C` loads the model specified in a0. The quoted code loops
loads models 0x00 to 0x0B (Mario to Ness). This pattern is used often in SSB,
such as when loading the CSS.

Note: This is over kill for the Results Screen, as at most, there will only
be four models  
Call before this code might be for allocating model space...?

### Winning Character Logo
At `0x80138D9C` :
```
80138D9C    lui   t0, 0x8014
      A0    lw    t0, 0x9C10(t0)
      A4    beq   s0, t0, ..B4
      A8    nop
      AC    jal   0x80132B20
      B0    nop
      B4    jal   0x80134FD0
```

I assume that the `beq` is checking for "No Contest." The first jal to routine
`0x80132B20` draws the logo.

#### Routine `0x80132B20`: Results.drawWinningLogo
##### Arrays
The first thing this routine does is put three separate arrays on the stack.
Each array is 0x30 bytes (as allocated): 12 member int32. As 12 member arrays,
there are obviously used as LUTs for character indexes.  

The first array is offsets from file 20 for the series logos.
I have no idea what the other two arrays are for...
The second array seems to be for "colorizing" the logo based on which player/
team won.
The third array is just the first array + 0x84... Used for "zooming" the logo?

It seems that these are offsets within resource-0035.bin (normally at `0x80171F40`), which unfortunately,
seems to be composed entirely of display list commands, and not bit-data images... :(

| Index | Character | Char Logo Offset| 2nd Array | 3rd Array |
|:-----:|:---------:|:---------------:|:---------:|:---------:|
|                  || 80138FE4        | 80139014  | 80139044  |
| 0x00  | Mario     | 00000990        | 00000000  | 00000A14  |
| 0x01  | Fox       | 000021D0        | 00001940  | 00002254  |
| 0x02  | DK        | 00001348        | 00000B00  | 000013CC  |
| 0x03  | Samus     | 00001860        | 00001470  | 000018E4  |
| 0x04  | Luigi     | 00000990        | 00000000  | 00000A14  |
| 0x05  | Link      | 00002520        | 000022B0  | 000025A4  |
| 0x06  | Yoshi     | 00002F10        | 00002690  | 00002F94  |
| 0x07  | C.Falcon  | 00003828        | 00002FF0  | 000038AC  |
| 0x08  | Kirby     | 00003E68        | 00003900  | 00003EEC  |
| 0x09  | Pikachu   | 00004710        | 00003F40  | 00004794  |
| 0x0A  | Jigglypuff| 00004710        | 00003F40  | 00004794  |
| 0x0B  | Ness      | 00005A00        | 00004840  | 00005A84  |

* Array 1 is loaded at `0x80132B34` until `0x80132B60`. Technically starts at `0x80132B24` to load base into t7
* Array 2 is loaded at `0x80132B60` until `0x80132B94`
* Array 3 is loaded at `0x80132B94` until `0x80132BC8`
* Array 4 (team colors?) loaded at `0x80132BC8` until `0x80132BEC`

##### Team Mode Check

At `0x80132BF4`, there is a `BNEL r0` that checks for Team Mode (0x01)

###### Branching:
Initial :
```
80132BE4    lui   v1, 0x8014
...
     BF0    lbu   v1, 0x9C14(v1)    // Results Screen Team Mode Flag (0x01)
     BF4    bnel  v1, r0, ..C24
     BF8    addiu at, r0, 0x0001
```
FFA Branch [`0x80132BFC`] :
```
80132BFC    jal   0x8013234C    // Result.getWinningPlayer
     C00    nop
     C04    or    s0, v0, r0    // save winning player
     C08    jal   0x80133148    // Result.getCharFromPlayer
     C0C    or    a0, v0, r0
     C10    lui   v1, 0x8014
     C14    sw    v0, 0x00D4(sp)
     C18    lbu   v1, 0x9C14(v1)
     C1C    sw    s0, 0x00D0(sp)
     C20    addiu at, r0, 0x0001
     C24    bnel  v1, at, ...C58  // merge FFA/Teams branches
     C28    or    a0, r0, r0
```

Teams Branch [`0x80132C24`]
```
80132BF8    addiu at, r0, 0x0001    // branch delay from `bnel`
...
     C24    bnel  v1, at, ...C58    // end of FFA branch; should always fail
     C28    or    a0, r0, r0        // always skipped
     C2C    jal   0x8013234C        // Result.getWinningPlayer
     C30    nop
     C34    jal   0x80133148        // Result.getCharFromPlayer
     C38    or    a0, v0, r0
     C3C    jal   0x80132A2C        // unknown routine; prob find winner on team or winning team
     C40    sw    v0, 0x00D4(sp)
     C44    sll   t4, v0, 0x2
     C48    addu  t9, sp, t4
     C4C    lw    t9, 0x0034(t9)
     C50    sw    t9, 0x00D0(sp)    // this player in FFA branch/prob team
     C54    or    a0, r0, r0
     C58    or    a1, r0, r0        // branches merge
```

###### Drawing the Logo

After `80132C58`, the game "allocates" and "links" some space (on the frame
buffer?). At `0x80132C68` the game restores the winning character from the
stack, and converts the index to words:
```
80132C68    lw    v1, 0x00D4(sp)
      6C    lui   t0, 0x8014
      70    lw    t0, 0xA058(t0)    // addr (8013A058) for resource-0035.bin
      74    sll   v1, v1, 0x2
      78    addu  t7, sp, v1
      7C    lw    t7, 0x00A0(t7)    // load from first array
      80    or    s0, v0, r0        // save "allocated" mem addr
      84    sw    v1, 0x0028(sp)    // save char index*4 on stack
      88    or    a0, v0, r0
      8C    or    a2, r0, r0
      90    jal   0x8000F120        // unknown routine
      94    addu  a1, t7, t0        // add char offset to file base
```

The key is at `0x80132C8C`

### Victory Theme and Character Name
##### Victory Theme
At `0x801389D4`, there are some initial checks for things like no contest, and
then the routine for find and playing the proper Victory BGM:
```
801389D4    lui   t5, 0x8014
     9D8    lw    t5, 0x9C10(t5)
     9DC    addiu at, r0, 0x0004
     9E0    lui   t6, 0x8014
     9E4    beq   t5, at, ...A0C    // check for "No Contest" (0x4)
     9E8    nop
     9EC    lw    t6, 0x9B78(t6)    // Results.Frame Timer...?
     9FO    addiu at, r0, 0x0078
     9F4    bne   t6, at, ...A0C    // If it is not 0x78 frames in, skip
     9F8    nop
     9FC    jal   0x80138714        // Results.playWinningCharBGM
     A00    nop
     A04    jal   0x80137854        // unknown routine
     A08    nop
     A0C    jal   0x80131E18        // jump points for checks; unknown routine
     A10    nop
```

Within `0x80138714` [Results.playWinningCharBGM; no arguments], the winning
character's index is retrieved. Then, there is a jump table [`0x80139868`;
located right after the victory strings] to "select" the proper BGM index
to pass to the ssb.playBGM routine [`0x80020AB4`].
Interestingly, there is a check for less than 0xC (aka only legal characters)

Routine Size:
* Begin : `0x80138714`
* End : `0x80138828`
* Size : 0x114 Bytes (276)

Summary Table:

| Char Index | Jump Table Addr | BGM Index |
|:----------:|:---------------:|:---------:|
| 0x0        | 80138750        | 0x0C      |
| 0x1        | 80138764        | 0x10      |
| 0x2        | 80138778        | 0x0E      |
| 0x3        | 8013878C        | 0x0D      |
| 0x4        | 80138750        | 0x0C      |
| 0x5        | 801387A0        | 0x15      |
| 0x6        | 801387B4        | 0x12      |
| 0x7        | 801387C8        | 0x13      |
| 0x8        | 801387F0        | 0x0F      |
| 0x9        | 801387DC        | 0x14      |
| 0xA        | 801387DC        | 0x14      |
| 0xB        | 80138804        | 0x11      |
| >= 0xC     | 80138818        | 0x0B      |

##### Playing Winning Character's Name FGM
`801389CC` : Check frame variable  
`80131EB0` : Routine  
* Starts by putting both arrays on the stack
  * really wasteful since they won't even be used most of the time due to the timer checks for timing the "phrase"
* `80131F20` : No Contest Check?
* `80131F70` : Team Mode Branch
* `80131FCC` : The call to ssb.playFGM that plays the character name?
  * On frame 0xD2 (after loading?), branch to 80131FB4
    * Get winning character index, and load FGM value from array on stack
      * Write down frame timings/ FGM indexes for each call. both teams/ffa/no contest

`80138FA8` :  FGM char array [12]  
`80138FD8` :  FGM team array [3]

##### Winning Phrase Timings and FGM Indices
###### No Contest
Starts at `0x80131F24`

| Frame | FGM Index | Phrase      |
|-------|-----------|-------------|
| 0x2   | 0x1F6     |---|
| 0x47  | 0x270     |---|

###### Free-for-All (Saying the Character's Name)
Check for not FFA at `0x80131F70`. Branch starts at `080131F78`

| Frame | FGM Index | Phrase      |
|-------|-----------|-------------|
| 0x51  | 0x216     |---|
| 0xD2  | Variable  | "Character Name" |
| 0x10E | 0x272     |---|

###### Team Mode?
Starts at `0x80131FEC`

| Frame | FGM Index | Phrase      |
|-------|-----------|-------------|
| 0x51  | Variable  | "Red/Blue/Green Team" |
| 0x82  | 0x215     |---|
| 0x96  | 0x272     |---|

### Printing Winning String
801345A4 func call


80139580 string pointer Red/Blue/Green

##### Calling the Routine
```
801345A4    lui   v0, 0x8014
      A8    lbu   v0, 0x9C14(v0)    // team mode byte (0x1)
      AC    bnel  v0, r0, ..5C8     //if (ffa /  !team-mode)
      B0    addiu at, r0, 0x1
      B4    jal   0x8013438C        //results.drawWinCharStr
      B8    nop
      BC
```

##### 0x8013438C Results.drawWinningCharacterString

The first part of this routine moves three arrays onto the stack:
* `0x801394F0` : string pointers
* `0x80139520` : string x positions float32
* `0x80139550` : string x scale float32

| Char Index | Str Pointer | Str X Position    | Str X Scale      |
|------------|-------------|-------------------|------------------|
| 0x0        | 801397D4    | 0x41F00000 [30.0] | 0x3F800000 [1.0] |
| 0x1        | 801397DC    | 0x42700000 [60.0] | 0x3F800000 [1.0] |
| 0x2        | 801397E0    | 0x428C0000 [70.0] | 0x3F800000 [1.0] |
| 0x3        | 801397E4    | 0x41C80000 [25.0] | 0x3F800000 [1.0] |
| 0x4        | 801397EC    | 0x42480000 [50.0] | 0x3F800000 [1.0] |
| 0x5        | 801397F8    | 0x425C0000 [55.0] | 0x3F800000 [1.0] |
| 0x6        | 80139800    | 0x41F00000 [30.0] | 0x3F800000 [1.0] |
| 0x7        | 80139808    | 0x41D80000 [27.0] | 0x3F333333 [0.7] |
| 0x8        | 80139818    | 0x42200000 [40.0] | 0x3F800000 [1.0] |
| 0x9        | 80139820    | 0x41F00000 [30.0] | 0x3F333333 [0.7] |
| 0xA        | 8013982C    | 0x41D80000 [27.0] | 0x3F19999A [0.6] |
| 0xB        | 80139838    | 0x42480000 [50.0] | 0x3F800000 [1.0] |

After loading these arrays, the routine calls Results.getWinningCharacter [`0x80134364`]. Then it calls Results.WriteStrBlockText [`0x80133F6C`]

void Results.WriteStrBlockText
* a0 : &str (null terminated)
* a1 : float32 Str X Position
* a2 : float32 Str Y Position
* a3 : pallet index
* sp + 0x10 : float32 Str X scale

For this, the y value is fixed at `0x43340000`[180.0]. Word height is roughly 40.0 units, as 140.0 looks to be 1 word up; 145.0 might work too (for time expanded Results screen stats table)


Once that routine is called, the routine calls `0x8013423C` with the winning character index in `a0`. This routine prints "WINS!" for both teams and ffa...

To print "WINS!" string, it needs an offset
801394B4 chars?
801394E4 teams?

| Char Index | Str X Position     |
|------------|--------------------|
| 0x0        | 0x432F0000 [175.0] |
| 0x1        | 0x43200000 [160.0] |
| 0x2        | 0x43160000 [150.0] |
| 0x3        | 0x43300000 [176.0] |
| 0x4        | 0x43230000 [163.0] |
| 0x5        | 0x43200000 [160.0] |
| 0x6        | 0x432A0000 [170.0] |
| 0x7        | 0x43320000 [178.0] |
| 0x8        | 0x43250000 [165.0] |
| 0x9        | 0x432C0000 [172.0] |
| 0xA        | 0x432D0000 [173.0] |
| 0xB        | 0x43200000 [160.0] |

###### String Write Pallet LUT
Array starts at `0x80139494`. Each color is 6 bytes long.
The first three bytes seem to be in RRGGBB hex, but the next 3 bytes are all `0xFFFFFF`. Are they some sort of mask channel...?

| Pallet | Hex           | Color  |
|--------|---------------|--------|
| 0x0    | FF0000 FFFFFF | Red    |
| 0x1    | 1200D9 FFFFFF | Blue   |
| 0x2    | 037300 FFFFFF | Green  |
| 0x3    | 6003D4 FFFFFF | Blue/Purple ["WINS!"] |
| 0x4    | 000000 FFFFFF | Monochrome |

0x8B008B FFFFFF works really nicely as a polygon color...

### Character Models and Animations
##### Zooms
80390D90 [12]
| Index | Hex      | Value |
|-------|----------|-------|
| 0x0   | 3FA00000 | 1.25  |
| 0x1   | 3F933333 | 1.15  |
| 0x2   | 3F800000 | 1.00  |
| 0x3   | 3F83D70A | 1.03  |
| 0x4   | 3F9AE148 | 1.21  |
| 0x5   | 3FAA3D71 | 1.33  |
| 0x6   | 3F866666 | 1.05  |
| 0x7   | 3F88F5C3 | 1.07  |
| 0x8   | 3F9C28F6 | 1.22  |
| 0x9   | 3F99999A | 1.20  |
| 0xA   | 3FA147AE | 1.26  |
| 0xB   | 3FA66666 | 1.30  |

routine here: 801338EC
```
..38EC  lui   0x8039
  38F0  addiu 0x0D90
```

Starts by loading array address, offsetting that by character address and then just loading the zoom value, then updating the model based on its pointer.

Since it passes the array by reference, and it uses a lui/add set to get the base address of the array, it's very easy to replace with our own, expanded array

##### Character Model Animation
`801334DC` : Results.AnimateCharacterModel
a0 : pointer to model info?
a1 : player index

Check for No Contest at 801334F0, branch is 801334F0 to 80133514 then branch to end (RA)

If there is no "No Contest," then the game runs Results.getTotalPlayers [`8013205C`]. There are separate branches for 2, 3, and 4 player games. If there happens to be more than 4 players, there's a fall-through branch that goes to the same path as 4 players.

###### 2 Players Branch
Check the called player's (a1) place. There are two branches here: a winning branch [`8013357C`] for place `0x0` and losing branch [`801335A0`] for place `0x1`.

####### Winning Branch
First, call Results.getCharFromPlayer to get the character index. Then, call getWinningAnimation [`8013345C`] with the character index, and use the returned value to animate the model (a0) [`8013345C`]

####### Losing Branch
First, call Results.getCharFromPlayer to get the character index. Then, call getClappingAnimation [`801334CC`] with the character index. This is odd, since  and use the returned value to animate the model (a0) [`8013345C`]

###### Results.getWinningAnimation [`0x8013345C`]
First, put the three animation indexes at '0x8013917C' on the stack:
```
8013917C  00010001  00010002  00010003
```
Then, there's a "branch" that checks for Kirby. If the character is kirby, only return from the first two animation indeces; if not, use all three.

Then, the routine calls getRandomInt with either 2 (Kirby) or 3 (Not Kirby). It uses the returned value to load an animation index, which is returns in v0.

###### Results.getClappingAnimation [`0x801334CC`]
Takes character index in a0. Doesn't need it at all, so this must be a vestige of something else. It puts a0 onto the stack, and returns `0x0001000A`. Here is the whole routine:
```
801334CC  lui   v0, 0x0001
      D0  sw    a0, 0x0000(sp)
      D4  jr    ra
      D8  ori   v0, 0x0005
```

##### Player Indicator Arrows Above Models
The routine at `0x80133C58` renders the triangle player indicator icon over the character model. The player index is passed in `a0`. `a1` is ????; the value in a1 is used to offset the 1 byte element array at `0x8012EF94`.

It copies three arrays onto the stack:

It calls a subroutine at `0x801339F4` that seems to do the actual positioning.


### Results Table
##### Player Indicator Triangle and Character Stock Icon

Routine starts at `0x8013549C`. The first part of the routine draws and positions the player indicator triangle.

Then, the routine gets an offset to the character icon [`0x801355BC`], draws it, and updates the icon's pallet to match the proper color.

The polygon circle icon is loaded at address in `0x80139C8C`. This should be a fixed pointer
