# 80171D38 - drawBeamSword
##### RAM Location: `0x80171D38`
##### ROM Location: `0xEC778`

Render the beam sword model. This routine is similar to the other routines that are like drawThrowingItem (`80171C7C`).
The only difference is the called function for drawing the actual beam sword model.

Ends at `0xEC830` / `0x80171DF0`

### Called Sub-Routines
* `0x80171C10` -> bool: checks for if a model needs to be rendered?
* `0x80014768` -> void : drawBeamSwordModel
* `0x801719AC` -> void : drawCollisionOverBomb
* `0x80171410` -> void : drawBombHitbox+Grabbox
