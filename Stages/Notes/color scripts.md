# Quick Notes on "Color Change Script"?

* Set of pointers
* that point to a list pointer
* list
  * Word value
  * Bits 31 - 25
    * srl? no top bit?
    * Check for < 0x17
    * index into jump table at `0x8003DE7C`
  * Bits 24 - 15
  * Bits 14 - 0
