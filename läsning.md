# Läsning
## Primär

https://libreboot.org/faq.html - Libreboot FAQ

https://www.raptorcs.com/blog/08212017001.php - Why Talos II

https://media.ccc.de/v/33c3-8314-bootstraping_a_slightly_more_secure_laptop - Föreläsning om Heads, bra introduktion till svårigheter, problem och lösningar till firmware och hårdvara. Alltså inbyggd-frihet.

## Ytterligare

En skala, med FSF RYF och Purism, som jämför olika frihetsgrader. Man inser vidden av problemet, då även FSF RYF accepterar non-free disk firmware. https://platen-software.de/tobias/pictures/purism-road-to-fsf-ryf-graphic-20150812-700x745-minifree.jpeg

Intels bok om Intel ME och dess användning http://www.apress.com/us/book/9781430265719

Intel ME enheter öppna mot internet https://www.shodan.io/report/PuIRbQpt

Ytterligare en guide för att deaktivera Intel ME https://news.ycombinator.com/item\?id\=15444607

Detecting BadBIOS, Evil Maids, Bootkits, and Other Firmware Malware (framför allt UEFI) - https://preossec.com/SeaGL-2017/ Dom nämner olika sätt man kan skydda sig på (sida ~60). Min känsla är att standard (libre/) coreboot installation är utsatt, delvis saknas (by default) skrivskydd på chippet efter skrivning samt malware skulle kunna flasha om för persistens (?). Vidare saknas “secure boot”-funktionalitet, utan det bör undersökas hur man gör detta på sin image med olika verktyg så att ENBART den bootar vissa / kräver lösenord. En annan fråga är hur kraftfullt Bash skalet är, då vem som helst kommer åt denna.

Periodic/Regular firmware scans: (eg: re-run CHIPSEC, dump ACPI tables, UEFI vars) PreOS Security has an upcoming ebook that covers this topic

Lagring och skärmar (MonitorDarkly) kör (propritär) mjukvara som kan bli ägd

Firmware is the new Black – Analyzing Past 3 years of BIOS/UEFI Security Vulnerabilities - https://github.com/rrbranco/BlackHat2017

>“My system is secure” is almost meaningless  • Against what? To what extent? (threat model)

tl;dr SMM for x86 : proprietary code is run from secret place in RAM, everything hidden from OS. When this can be owned or backdoored = fun
