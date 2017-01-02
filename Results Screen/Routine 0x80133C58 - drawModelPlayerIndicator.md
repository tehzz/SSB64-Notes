## Analysis of Routine `0x80133C58`
### drawModelPlayerIndicator

This routine draws the 1P/2P/3P/4P/CPU triangle indicator above the character model on the
results screen.


### Routine Inputs
a0 : u8 player index
a1 : u8 MAN/CPU and Team Enum <1 : Man; 4 : CPU> || <1 : Red; 2: Blue; 3: Green; 4: CPU>

### Routine Outputs
void

### Code Overview
* Put a bunch of color byte arrays on the stack.
* Draw either the 1-4P indicator image, or the "CPU" indicator image
* Color that image based on Team, CPU Status, or Player Number
* Call positionModelPlayerIndicator [`0x801339F4`] to correctly position the image
* Returns void to caller
