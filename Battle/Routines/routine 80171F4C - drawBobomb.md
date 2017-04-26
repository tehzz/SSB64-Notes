# 80171F4C - drawBobomb
##### RAM Location: `0x80171F4C`
##### ROM Location: `0xEC98C`

Render the bob-omb. This routine is similar to the other routines that are like drawThrowingItem (`80171C7C`).
The only difference is the called function for drawing the actual bomb-omb sprite.

Ends at `0xECA44` / `0x80172004`

### Called Sub-Routines
* `0x80171C10` -> bool: checks for if a model needs to be rendered?
* `0x80171DF4` -> void : drawBobombModel
* `0x801719AC` -> void : drawCollisionOverBomb
* `0x80171410` -> void : drawBombHitbox+Grabbox
