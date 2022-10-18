## Intro
Sidan [h-node](https://h-node.org/home/index/en) för söker samla all fri hårdvara. Även [FSF RYF](https://www.fsf.org/resources/hw/endorsement/respects-your-freedom) som är en kortare mer koncis lista.

Notera dock att det finns olika frihetsgrader och bara för att din dator/telefon inte behöver propritärmjukvara vet du inte vad hårdvaran själv gör (exempelvis en fingeravtrycksläsare). Således är en enhet som *faktiskt* kör fri kod, eller som t om är öppen hårdvara, friare och mer pålitlig än många av de som nämns här. Därav målet med inbyggd frihet, öppen firmware. Alltså, även om vi rekommenderar vissa enheter finns det fortfarande element som är stängda och utan alternativ idag. Telefoner gräver GSM nät, hårddiskar har okänt firmware, processorerna själva har odokumenterade funktioner och tillverkningsprocess, etc.

## Äldre enheter (2007-2012)
Libreboot och Replicant enheter fortfarande konkurrenskraftiga i kategorin blobb- och [[IME|https://en.wikipedia.org/wiki/Intel_Management_Engine]]-fria. 

Mer information om Replicant och Libreboot jobbas på under [våra guider](../guider). Dessa enheter funkar generellt bra men kräver lite jobb att installera ('flasha') om man inte [köper färdigt](https://tehnoetic.com).

#### Dator - Laptop - Server
[Libreboot](Libreboot.org) med enheter som X200 (Intel, Thinkpad) och KGPE-D16 (AMD, server).

#### Telefoner
[Replicant](Replicant.us) med enheter som Galaxy S2, S3 eller Note 8.0. Deras rekommendation finns [här](https://www.replicant.us/freedom-privacy-security-issues.php#recommendations).

____________________
## Nya enheter

Enheter som går att för-/beställa, dock har vi inte testat dom.

#### Server:

<https://www.raptorcs.com/content/TL2WK2/intro.html> - Talos II

#### NAS - blobbfri

<https://www.crowdsupply.com/gnubee/personal-cloud-1>

<https://www.crowdsupply.com/gnubee/personal-cloud-2>

#### Arduino/Raspberry Pi-substitut - open hardware

<https://groupgets.com/campaigns/353-lofive-risc-v>

<https://www.crowdsupply.com/sifive/hifive1/>

<https://www.olimex.com/Products/OLinuXino/open-source-hardware> - är ett antal öppen hårdvara produkter, verkar som de friaste.

<http://beagleboard.org/pocket> - Rasberry pi zero-liknande produkt. De har olika produkter, men deras beagle bone black har varit buggig för många.

<http://www.banana-pi.org/product.html>

#### Telefon - Kräver blobbar
Det finns en mängd frihetsproblem med telefoner vilka vi diskuterar [här](../problem)

Nedan återfinns en lista över telefoner vilka påstås erbjuda en plattform med öppen källkod, dock med blobbar.

<https://puri.sm/shop/librem-5/>

<https://www.pine64.org/pinephone/>

<https://www.crowdsupply.com/arsenijs/zerophone>

Fairphone 3


#### Router / Switch - Kräver blobbar?

<http://www.banana-pi.org/r2.html>

<https://www.crowdsupply.com/traverse-technologies/five64/>

#### Laptop - open hardware, RISC-V
[Libre RISC V](https://www.sweclockers.com/nyhet/27683-klartecken-for-saker-processor-med-risc-v) även på [crowdsupply](https://www.crowdsupply.com/libre-risc-v/m-class)

#### Laptop - Kräver blobbar, men kör åtminstonde inte proprietär UEFI/BIOS
<https://www.olimex.com/Products/DIY-Laptop/KITS/TERES-A64-BLACK/open-source-hardware>

<https://puri.sm/products/>

Novena Open Laptop

#### Two-Factor-Authentication
Nitrokey <https://infosec-handbook.eu/blog/yubikey4c-nitrokeypro/>

#### FPGA

<http://papilio.cc/> ?

**iCE40** -

  <https://wiki.debian.org/FPGA/Lattice>

  <http://www.clifford.at/icestorm/>

  <https://media.ccc.de/v/32c3-7139-a_free_and_open_source_verilog-to-bitstream_flow_for_ice40_fpgas#video>
  
__________________________________________________________
#### Kommentarer

Nästan allt ARM-baserat kräver blobbar till grafikkretsen utom Vivante som kan använda [[etnaviv|https://github.com/etnaviv]].
Undvik Raspberry pi som kräver ofri mjukvara för uppstart. Se [[FSF:s sida om enkortsdatorer|https://www.fsf.org/resources/hw/single-board-computers]].

OLinuXino verkar ha de mest frihetliga produkterna, därefter Beaglebone. Banana Pi verkar okej också?
Dessa produkter må ha fri hårdvarudokumentation men i slutändan använder de ett ARM-chip som kräver blobbar för grafiken.
Således behöver vi se RISC-V som ersättare till ARM och OpenPOWER som ersättare till x86 framöver. 

Udoo har ett par brädor men dom pushar ut Intel x86 på ny produkt, verkar inte lika fritt.
Pine64 verkar ha tveksamt mjukvarustöd, verkar inte lika fritt.

__________________________________________________________

#### Kommentarer
Vi borde ha en lista över hårdvara att undvika: x86, Intel, AMD, Raspberry pi etc.

__________________________________________________________
#### Div
<https://en.wikipedia.org/wiki/List_of_open-source_hardware_projects>

<https://en.wikipedia.org/wiki/Open-source_computing_hardware>
