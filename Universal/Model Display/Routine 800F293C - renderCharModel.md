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
