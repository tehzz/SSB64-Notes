# 8012C520 - Lookup Table for Stage Indices
* Total Size : 0x148 bytes
* Entry Size : 0x8 bytes
* Number of Entries : 0x29 (41)
* RAM : `0x8012C520`
* ROM : `0xA7D20`

This is the table that converts from the stage index value to the actual stage file. Changing a file here can easily effect which stage is loaded. An entry is composed of two 32bit words. The first word is the stage "base file" from the file table. What does this second word mean again? 

| Stage Index | Stage File  | Second Word | Stage Name      |
|-------------|-------------|-------------|-----------------|
| 0x0         | 0x103       | 0x14        | Peach's Castle  |
| 0x1         | 0x106       | 0x14        | Sector Z        |
| 0x2         | 0x105       | 0x14        | Congo Jungle    |
| 0x3         | 0x101       | 0x14        | Planet Zebes    |
| 0x4         | 0x109       | 0x14        | Hyrule Castle   |
| 0x5         | 0x107       | 0x14        | Yoshi's Island  |
| 0x6         | 0x0FF       | 0x14        | Dream Land      |
| 0x7         | 0x108       | 0x14        | Saffron City    |
| 0x8         | 0x104       | 0x14        | Mushroom Kingdom|
| 0x9         | 0x100       | 0x14        | Kirby Beta 1    |
| 0xA         | 0x102       | 0x14        | Kirby Beta 2    |
| 0xB         | 0x10B       | 0x00        | How-to-Play Stage |
| 0xC         | 0x10E       | 0x14        | Yoshi's Island 1P |
| 0xD         | 0x10D       | 0x14        | Metal Cavern    |
| 0xE         | 0x10C       | 0x14        | Battlefield     |
| 0xF         | 0x127       | 0x00        | Race to the Finish! |
| 0x10        | 0x10A       | 0x00        | Final Destination |
| 0x11        | 0x10F       | 0x00        | Targets - Mario |
| 0x12        | 0x110       | 0x00        | Targets - Fox   |
| 0x13        | 0x111       | 0x00        | Targets - DK    |
| 0x14        | 0x112       | 0x00        | Targets - Samus |
| 0x15        | 0x113       | 0x00        | Targets - Luigi |
| 0x16        | 0x114       | 0x00        | Targets - Link  |
| 0x17        | 0x115       | 0x00        | Targets - Yoshi |
| 0x18        | 0x116       | 0x00        | Targets - C.Falcon |
| 0x19        | 0x117       | 0x00        | Targets - Kirby |
| 0x1A        | 0x118       | 0x00        | Targets - Pikachu |
| 0x1B        | 0x119       | 0x00        | Targets - Jigglypuff  |
| 0x1C        | 0x11A       | 0x00        | Targets - Ness  |
| 0x1D        | 0x11B       | 0x00        | Platforms - Mario |
| 0x1E        | 0x11C       | 0x00        | Platforms - Fox |
| 0x1F        | 0x11D       | 0x00        | Platforms - DK  |
| 0x20        | 0x11E       | 0x00        | Platforms - Samus |
| 0x21        | 0x11F       | 0x00        | Platforms - Luigi |
| 0x22        | 0x120       | 0x00        | Platforms - Link  |
| 0x23        | 0x121       | 0x00        | Platforms - Yoshi |
| 0x24        | 0x122       | 0x00        | Platforms - C.Falcon  |
| 0x25        | 0x123       | 0x00        | Platforms - Kirby |
| 0x26        | 0x124       | 0x00        | Platforms - Pikachu |
| 0x27        | 0x125       | 0x00        | Platforms - Jigglypuff  |
| 0x28        | 0x126       | 0x00        | Platforms - Ness  |
