# Structure VSRecordData
* Base: 0x800A44E0
* Size: 0x5C (92) bytes

This holds the vs mode battle information for the Data -> VS Record screen.
Persumably, there are 12 of these structures; one for each of the standard 12 characters.

## Character Offsets
| Character | Index | RAM Offset  |
|-----------|-------|-------------|
| Mario     | 0x0   | 800A44E0    |

## Known Fields
### + 0x0 to 0x18 : KOs on Other Characters
| Base Offset | Type | Field                  |
|-------------|-----:|------------------------|
| + 0x0       | u16  | KOs on Mario           |
| + 0x2       | u16  | KOs on Fox             |
| + 0x4       | u16  | KOs on DK              |
| + 0x6       | u16  | KOs on Samus           |
| + 0x8       | u16  | KOs on Luigi           |
| + 0xA       | u16  | KOs on Link            |
| + 0xC       | u16  | KOs on Yoshi           |
| + 0xE       | u16  | KOs on C.Falcon        |
| + 0x10      | u16  | KOs on Kirby           |
| + 0x12      | u16  | KOs on Pikachu         |
| + 0x14      | u16  | KOs on Jigglypuff      |
| + 0x16      | u16  | KOs on Ness            |

### + 0x18 to 0x2C : Counting Statistics
| Base Offset | Type | Field                  | Limits |
|-------------|-----:|------------------------|:-------|
| + 0x18      | u32  | Time Used (seconds)    | < 36,000,000 |
| + 0x1C      | u32  | Total Damaged Dealt    | < 1,000,000 |
| + 0x20      | u32  | Total Damage Received  | < 1,000,000 |
| + 0x26      | u16  | Total SDs              | < 10,000 |
| + 0x28      | u16  | Games Played           | i++ |
| + 0x2A      | u16  | Total Players in Match | u16? |

### 0x2C +

 \>= + 0x2C holds the sum of the number of players in a game that include the character. EG, base + 0x2C is a halfword for the total number of players in games that include both Mario, and whomever your base character is.

Then, there is another set of character half-words for total games against that character
These two values are used to calc the individual character "AVG"

* Take Games Played / Total Players to calculate table "AVG"
