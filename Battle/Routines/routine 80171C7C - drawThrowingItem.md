# 80171C7C - drawThrowingItem
##### RAM Location: `0x80171C7C`
##### ROM Location: `0xEC6BC`

This draws the model for thrown items (like a beam-sword, or a barrel, etc.), as well as for some stage hazards (Pokemon on Saffron City)

## Parameters
### Inputs
* a0 : pointer to some struct (unique for each item)
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
* `0x80171C10` -> bool : checks for if a model needs to be rendered?
* `0x80014038` -> void : drawNormalModel?
* `0x801719AC` -> void : drawCollisionOverBomb
* `0x80171410` -> void : drawBombHitbox+Grabbox


## Analysis
The major difference that I see between this routine and drawLinksBomb (`8017224C`) is a different model rendering routine.
The data structure passed in a0 derives its hitbox state from a player at item instantiation or when picked-up up by another player. (Hitbox "state" is at `base + 0x0374`)

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
        beq   v0, r0, epilouge    ; skip the rest of the routine if false
        lw    a1, 0x0020(sp)

load_hitbox_state (0x80171CA4):
        lw    v0, 0x0374(a0)      ; u32 hitbox value from player (who spawned projectile?)
        beq   v0, r0, model_path
        nop

unknown_value_check (0x80171CB0): ; this might check if there's an active hitbox?
        lw    t6, 0x02CC(a0)
        addiu at, r0, 0x0003      ; setting up for collision comparison
        sll   t8, t6, 0x11
        bgez  t8, check_if_hb_or_collision
        nop

model_path (0x80171CC4):
        jal   0x80014038          ; called with initial input pointer in a0
        or    a0, a1, r0
        b     epilogue + 0x4
        lw    ra, 0x0014(sp)

check_if_hb_or_collision (0x80171CD4):
        bnel  v0, at, hitbox_path ; v0 = hitbox enum || at = 0x3
        lw    t9, 0x0248(a0)      ; hitbox_path only

collision_path (0x80171CDC):
        or    a0, a1, r0
        jal   0x80014038          ; draw item model
        sw    a1, 0x0020(sp)
        jal   0x801719AC          ; render the collision diamond?
        lw    a0, 0x0020(sp)
        b     epilogue + 0x4
        lw    ra, 0x0014(sp)

orphaned_code? (0x80171CF8):
        lw    t9, 0x0248(sp)

hitbox_path (0x80171CFC):
        bnez  t9, draw_hitbox     ; is hitbox active?, draw only hitbox
        nop
        lw    t0, 0x010C(a0)
        bnez  t0, draw_hitbox     ; another check?
        nop
  ; this is a failure case? Call to draw normal item model, and not the hitbox/hurtbox
        jal   0x80014038          ; draw item model?
        or    a0, a1, r0
        b     epilogue + 0x4
        lw    ra, 0x0014(sp)

draw_hitbox (0x80171D20):
        jal   0x80171410
        or    a0, a1, r0

epilogue (0x80171D28):
        lw    ra, 0x0014(sp)
        addiu sp, sp, 0x20
        jr    ra
        nop                       ; 0x80171D34
```
