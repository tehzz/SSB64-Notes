# 0x0E Battlefield
`0xE 	0x10C 	0x14 	Battlefield`

## File 0x10C (268)
* File Pointer List First Offset
  * `+0x98` (152)
* Req File(s) List First Offset
  * `+0x24` (36)


### Internal File Offsets
00) @ `0x98` -> offset + `0x00`
  * `FFFF 0000`


### Req Files + Offsets
00) `0x0074` (116) @ offset `+0x24`
  * Points to File `0x74` + `0x4170`  
01) `0x0074` (116) @ offset `+0x54`
  * Points to File `0x74` + `0x4388`
02) `0x0061` (97)  @ offset `+0x5C`
  * Points to File `0x61` + `0x26C88`
  * This is the background image

## File Structure

| Offset | Type     | Value     | Description |
|--------|----------|-----------|-------------|
| 0x5C   | ssb_ptr  | FFFF 9B22 | This points to external file `0x61`/97. In RAM, it points the image footer of the background image |
| 0x90   | u32      | 00000024  | BGM index (u8) |
