# 80131D80 - printString
* RAM Routine Location: `80131D80`

This is the location of the print string function on the Stage Select Screen. In NTSC-J, this routine (persumably) writes the english name strings. It is not called in NTSC-U, but the code is still present.

### Inputs
Should look up this function in NTSC-J to check
* a0 :  pointer to 2d image struct
* a1 :  *char string to print
* a2 :  f32 x position?
* a3 :  f32 y position?

any scale floats...?

### Outputs
unknown....
