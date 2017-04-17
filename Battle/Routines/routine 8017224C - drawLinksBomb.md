# 8017224C - drawLinksBomb
##### RAM Location: `0x8017224C`
##### ROM Location: `0xECC8C`

This renders the model / hitbox / collision hitbox for, seemingly, only Link's bomb.
The routine is very similar to drawThrowingItem (`80171C7C`), but it calls different rendering sub-routines

## Parameters
### Inputs
* a0 : pointer to some struct

### Outputs
* void

### Stack
```
Size   ->  0x20
+ 0x14  <- ra
+ 0x1C  <- *(a0 + 0x84)
+ 0x20  <- a0
```

### Called Sub-Routines
* `0x80171C10` -> bool: checks for if a model needs to be rendered?
* `0x80172008` -> void : drawBombNormalModel
* `0x801719AC` -> void : drawCollisionOverBomb
* `0x80171410` -> void : drawBombHitbox+Grabbox

## Analysis
This routine seems to do more "work" than the semi-equivalent routine for owned projectiles (0x80167520)

## Full Routine
```Assembly
prologue (8017224C):
        addiu sp, sp, -0x20
        sw    ra, 0x0014(sp)
        or    a1, a0, r0
        lw    a0, 0x0084(a0)

        sw    a1, 00020(sp)
        jal   0x80171C10          ; routine to check if a model needs to be drawn?
        sw    a0, 0x001C(sp)
        lw    a0, 0x001C(sp)
        beq   v0, r0, epilouge    ; so, 0x80171C10 returns a bool?
        lw    a1, 0x0020(sp)

load_hitbox_state:
        lw    v0, 0x0374(a0)      ; u32 hitbox value from player (who spawned projectile?)
        beq   v0, r0, model_path
        nop

unknown_value_check (80172280):   ; this might check if there's an active hitbox?
        lw    t6, 0x02CC(a0)
        addiu at, r0, 0x0003      ; setting up for collision comparison
        sll   t8, t6, 0x11
        bgez  t8, check_if_hb_or_collision
        nop

model_path (80172294):
        jal   0x80172008          ; called with initial input pointer in a0
        or    a0, a1, r0
        b     epilogue + 0x4
        lw    ra, 0x0014(sp)

check_if_hb_or_collision (801722A4):
        bnel  v0, at, hitbox_path ; v0 = hitbox enum || at = 0x3
        lw    t9, 0x0248(a0)      ; hitbox_path only

collision_path (801722AC):
        or    a0, a1, r0
        jal   0x80172008          ; draw projectile model
        sw    a1, 0x0020(sp)
        jal   0x801719AC          ; this must render the collision diamond
        lw    a0, 0x0020(sp)
        b     epilogue + 0x4
        lw    ra, 0x0014(sp)

orphaned_code? (801722C8):
        lw    t9, 0x0248(sp)

hitbox_path (801722CC):
        bnez  t9, draw_hitbox     ; is hitbox active?, draw only hitbox
        nop
        lw    t0, 0x010C(a0)
        bnez  t0, draw_hitbox     ; another check?
        nop
  ; this code draws only the model instead
        jal   0x80172008
        or    a0, a1, r0
        b     epilogue + 0x4
        lw    ra, 0x0014(sp)

draw_hitbox (801722F0):
        jal   0x80171410
        or    a0, a1, r0

epilogue (801722F8):
        lw    ra, 0x0014(sp)
        addiu sp, sp, 0x20
        jr    ra
        nop
        nop
        nop                       ; 0x8017230C
```
