# 800F293C - renderCharModel
Renders the actual character model every frame. Probably does other things too

## Parameters
### Inputs
* a0 : Pointer (player struct pointer?)


a0 + 0x84 -> s8 [pointer to player data struct]



## Notes
### Hitbox Display
* Enum:
```
enum model {
    Normal = 0,
    Hitbox = 1,
    Hitbox_Solid = 2,
    Collision = 3
};
```
* Initial check for hitbox starts at `800F2C00`:
  * s8 = Pointer to player data struct

```
800F2C00  lw    v1, 0x0B4C(s8)
    2C04  beql  v1, r0, ..F2C18
    2C08  lw    s0, 0x0000(s4)
    2C0C  bne   v1, at, ..F330C
    2C10  lui   t7, 0x8004
    2C14  lw    s0, 0x0000(s4)
```
* So, each of the main three model enums (0, 3, _) have distinct paths that align with certain model draws:
  * 0: Normal Model
    * Start at 800F2C14
    * Ends at 800F2FC4
  * 3: Collision
    * Starts at 800F2FD8?
    * Ends at 800F3304-800F3308?
  * 1 / 2: Hurtboxes and Hitboxes
    * Starts at 800F330C
    * Ends at ~800F3654?
    * The actual hurtbox seems to be drawn by routine at 800F2584
      * There is then a couple of bytes store to "unaffix" the hurtbox...
    * Hitboxes seem to be from 800F3358 to 800F3654

## Hurtbox "Mooring"
The hurtbox (yellow cubes) can be "un-moored" from where the model should be displayed.
This is what causes the models to bunch in middle of the screen on the character select screen, for instance.

``` assembly
; 0x800F2C0C
        bne   v1, at, 800F330C      ; jump ahead to hurt/hitbox code path
        lui   t7, 0x8004
; .....
; 0x800F330C
        lw    t7, 0x6A58(t7)
        addiu at, r0, 0x03EA        ; this is the key "mooring" value
        lw    t5, 0x0000(t7)
        bnel  t5, at, 0x800F3334    ; if **t7 == 0x03EA, moor hurtbox to proper position
        lw    t3, 0x08E8(s8)
; 0x800F3320 (Moored Path)
        jal   0x800F2584
        lw    a0, 0x0074(s6)
        b     0x800F3358
        nop
        lw    t3, 0x08E8(s8)        ; un-reachable
; 0x800F3334 (Unmoored Path)
        addiu t9, r0, 0x001A
        lw    t6, 0x0058(t3)
        sb    t9, 0x0004(t6)
        jal   0x800F2584
        lw    a0, 0x0074(s6)
        lw    t8, 0x08E8(s8)
        addiu t4, r0, 0x004B
        lw    t7, 0x0058(t8)
        sb    t4, 0x0004(t7)
; 0x800F3358 (Paths recombine)
```
