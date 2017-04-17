# 80167520 - drawProjectile
##### RAM Location: `0x80167520`
##### ROM Location: `0xE1F60`

Draw the model for a character's projectile attacks (Mario Fireball, Link Boomerang, etc.) and projectiles from some items (stars, some pokemon projectiles, etc.).
This might also draw things like homerun bat/star rod/sword swing.

## Parameters
### Inputs
* a0 : pointer to some struct (which eventually points to player struct)
* a1 : function pointer (routine of how to draw projectile)
### Outputs
* void

### Stack
```
Size   ->  0x20
+ 0x18  <- s0 (preserve initial)
+ 0x1C  <- ra
+ 0x24  <- a1
```

### Called Sub-Routines
* `0x80167454` -> void : renderProjModel1
* `0x801674B8` -> void : renderProjModel2
* `0x801671F0` -> void : renderProjCollisionDiamond?
* `0x80166E80` -> void : renderProjHitbox?

## Analysis

## Full Routine
```Assembly
prologue:                         ; (0x80167520)
        addiu sp, sp, -0x20
        sw    ra, 0x001C(sp)
        sw    s0, 0x0018(sp)
        sw    a1, 0x0024(sp)

load_player_hitbox_state:         ; (0x80167530)
        lw    v0, 0x0084(a0)
        or    s0, a0, r0
        addiu at, r0, 0x0003
        lw    v1, 0x02BC(v0)
check_for_collision_mode:         ; (80167540)
        bne   v1, at, check_for_normal_model
        nop
render_collision:                 ; (0x80167548)
        jal   0x80167454          ; first necessary routine for drawing normal model
        nop
        lw    t9, 0x0024(sp)      ; restore input a1
        or    a0, s0, r0
        jalr  ra, t9              ; call input a1
        nop
        jal   0x801674B8          ; second necessary routine for drawing normal model
        nop
        jal   0x801671F0          ; draw collision diamond
        or    a0, s0, r0
        b     epilogue
        lw    ra, 0x001C(sp)

check_for_normal_model:           ; (0x80167578)
        beq   v1, r0, render_normal_model
        nop

check_for_active_hitbox:        ; (0x80167580)
        lw    t6, 0x0100(v0)    ; v0 is pointer to player struct
        bnez  t6, render_hitbox ; branch if active? else fall-through to render normal model
        nop
render_normal_model:            ; (0x8016758C)
        jal   0x80167454        ; first necessary routine for drawing normal model
        nop
        lw    t9, 0x0024(sp)    ; restore input a1
        or    a0, s0, r0
        jalr  ra, t9            ; call input a1
        nop
        jal   0x801674B8        ; second necessary routine for drawing normal model
        nop
        b     epilogue
        lw    ra, 0x001C(sp)

render_hitbox:                  ; (0x801675B4)
        jal   0x80166E80        ; draw hitbox (can this draw hurtboxes? does it need to?)
        or    a0, s0, r0
        lw    ra, 0x001C(sp)

epilogue:                       ; (0x801675C0)
        lw    s0, 0x0018(sp)
        addiu sp, sp, 0x0020
        jr    ra
        nop                     ; 0x801675CC
```
