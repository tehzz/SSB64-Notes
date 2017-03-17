## Analysis of Routine `0x80131B78`
### countHmnPlusCpuPlayers

### Inputs
void

### Outputs
v0 : u8 total number of players in game

Very short, 6 instruction routine:
```
lui   v1, 0x800A
addiu v1, v1, 0x4D08
lbu   t6, 0x0004(v1)
lbu   t7, 0x0005(v1)
jr    ra
addu  v0, t6, t7
```

The first (+0x4) is MAN players and the second (+0x5) is CPU players
