## Övrigt 

### Konversation om firmware säkerhet

#### Backdoors be gone!

https://media.ccc.de/v/33c3-8314-bootstraping_a_slightly_more_secure_laptop

Lycka till att ha säkert OS när firmwaret från dina olika enheter på moderkortet kan skriva över ditt boot flash, etc. (Beroende på hotbild..)


#### Librem?

Asså det är givetvis inte perfekt och x86 är shit. Men som jag förstår det kör dom coreboot med halvdödad IntelME (eller??), så det är mkt bättre än andra moderna laptops out of the box. Bonus är om dom kommer inkludera Heads som payload.

idèerna från heads talket är dock fortfarande aktuella

tpm och skydda boot flash vore nice

purism verkar dock köra fett moderna laptops så oklart hur kräsna dom är med frihet..

fördelen med moderna laptops är att dom har IOMMU så du slipper bli hackad av varenda PCIE enhet


#### Intel?
"Panic level: 8999 Memory Reference Code: (Sandybridge and newer). This is code that runs on the CPU and initializes RAM. Can be reverse engineered with enough persistence, but not done so far. The "Memory Reference Code", initializes memory and USB power states (other functions are yet unknown). It is provided by Google, and is wrapper for Intel PEI modules. (see Intel_Native_Raminit for sandy/ivybridge native coreboot raminit)"

Google!?


#### AMD?
AMD är mer eller mindre i samma situation fast inte reverse engineerat till samma grad

https://libreboot.org/faq.html#amd

https://www.coreboot.org/Binary_situation

"PSP is AMD's analog of Intel's ME with a few implementation differences, in laymans terms the panic level is the same and it is present on newer AMD processors (see above) including FM2+ and Zen (AM4), it is also present on the die but supposedly not activated on various older models as well. "

#### x86?
"In this talk, we'll demonstrate how page fault analysis and some creative processor fuzzing can be used to exhaustively search the x86 instruction set and uncover the secrets buried in your chipset. "

https://www.youtube.com/watch?v=KrksBdWcZgQ

P R O P R I E T A R Y

I N S T R U C T I O N 

S E T

">trusting documentation"
