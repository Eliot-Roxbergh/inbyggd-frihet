See även [rekommenderade system](rekommenderat.md)

## Frihets- och integritetsproblem med mobiltelefoner

Mobiltelefoner har ett visst lagkrav att inkludera spårningsenheter (se t.ex. [hit](https://ec.europa.eu/info/law/better-regulation/initiative/1979/publication/322690/attachment/090166e5bedd7e5d_en)) samt de kräver propritära komponenter för telefonin (basband processor).
Således, är open-source försök begränsade och bör åtminstonde erbjuda hårdvaruswitchar för att säkert kunna stänga av känsliga sensorer: så som, mikrofon och GPS.

Den fria Androiddistributionen [Replicant](https://www.replicant.us) har en läsvärd [artikel om frihets- och integritetsproblem](https://www.replicant.us/freedom-privacy-security-issues.php) när det gäller mobila enheter. Texten nedan är en översättning av några centrala stycken (tillgänglig under [CC BY-SA 3.0](https://creativecommons.org/licenses/by-sa/3.0/) och tagen från <https://wiki.fripost.org>):

> En allt större del av datoranvändningen sker från mobila enheter såsom telefoner och surfplattor och de är därför särskilt viktiga ur frihets- och integritetshänseende. Dessa enheter är fullfjädrade datorer med kraftfull hårdvara och de kör fullständiga operativsystem som gör det möjligt att både förändra den medföljande mjukvaran och installera nya program. Man kan alltså köra fri programvara på dem. Samtidigt används mobila enheter till kommunikation och utöver att de lagrar användardata  tillhandahåller hårdvaran funktioner som är känsliga när det gäller integriteten -- GPS, kamera, mikrofon m.m. -- eftersom de kan användas till att spionera på användaren.
>
> Modemsystemet i en mobiltelefon är alltid proprietär. (Det existerar en fri GSM-mjukvara som heter OsmocomBB men den fungerar i dagsläget endast på gamla "osmarta" telefoner, kräver en värddator för att fungera och är inte certifierad för att användas i publika nätverk.) Samtidigt är modemet helt centralt ur integritetshänseende: det är nästan alltid anslutet till GSM-nätverket vilket gör att det kan fjärrstyras. Modemet kan vara mer eller mindre dåligt ur integritetssynpunkt beroende på vilken typ av hårdvara som det har tillgång till och kan kontrollera, dvs. hur isolerat modemet är från den övriga enheten. En dåligt isolerad enhet ger modemet åtkomst till och kontroll över nyckelkomponenter i hårdvaran såsom arbets- och lagringsminnet, GPS-mottagaren, kameran, in- och utgångar samt mikrofonen. Denna situation är fruktansvärt dålig ur integritetshänseende eftersom det finns en uppsjö av sätt att effektivt spionera på användaren på distans genom telefonnätverket. Mobiloperatören kan göra det men även angripare som sätter upp falska basstationer. När modemet å andra sidan är välisolerat från den övriga enheten och endast kommunicerar direkt med systemkretsen kan det bara komma åt till exempel mikrofonen när systemkretsen tillåter det. Det är då strikt begränsat till vad det verkligen behöver vilket avsevärt minskar risken för att bli utsatt för spionage. Modemisolation löser inte några av frihetsproblemen men är ett viktigt framsteg ur integritetshänseende. Tyvärr är det nästintill omöjligt att vara säker på att att modemet faktiskt är isolerat eftersom det inte går att lita på dokumentationen till ofri hårdvara. Däremot är det möjligt att veta när modemet inte är isolerat, alltså när det går att visa att modemet faktiskt kan komma åt hårdvara på ett sätt som låter det spionera på användaren.
>
> Den största delen av mjukvaran på en mobil enhet är operativsystemet och det körs på huvudprocessorn. Operativsystemet har åtkomst till de flesta inbyggda kretsarna såsom kamera, mikrofon, I/O liksom användarens data och kommunikation. Det är den mest kritiska delen ur integritetshänseende och även mycket viktig ur frihetssynpunkt eftersom det interagerar direkt med användaren och har kännedom om kommunikationen med hårdvaran. Många mobila operativsystem är mestadels fria, t.ex. Android, Ubuntu Touch och Tizen, eftersom de använder Linuxkärnan, en fri stomme samt innehåller fria grundapplikationer. HAL, den del som sköter kommunikationen mellan applikationer och hårdvara, är dock allt som oftast proprietär (det varierar från enhet till enhet) och de distribueras också tillsammans med proprietär firmware som vid uppstart laddas in i de olika integrerade kretsarna (t.ex. wifi och blåtand). All proprietär programvara i systemet utgör en integritetsrisk eftersom den kan vara en bakdörr som kan användas för att ta kontroll över resten av systemet. Inga av dessa mestadels fria operativsystem har tydliga riktlinjer som tar avstånd från all ofri programvara. Det gör dock Replicant.


## Frihets- och integritetsproblem med dagens datorsystem

### Intel ME / AMD PSP
Använd *inte* AMD system efter 2012. Vissa AMD system stöds av Libreboot, dessa är däremot OK.

Undvik helst Intel system, dock kan det största problemet ibland åtgärdas delvis. På vissa Intel system går det att ta bort delar av Intel ME genom att använda [me_cleaner](https://github.com/corna/me_cleaner/wiki). Vidare stöds vissa Intel system av Libreboot, dessa är rekommenderade, i dessa fall är Intel ME helt avaktiverat.


### UEFI (+Secure Boot)
På grund av frihets- och säkerhetsproblematik rekommenderas *inte* UEFI. Använd istället Coreboot (/Libreboot) eller U-Boot.

## Frihets- och integritetsproblem som berör all hårdvara
### Proprietär firmware
### Proprietära drivrutiner för hårdvaran
### Proprietär arkitektur / design

_______
De största problemen idag vi inte har diskuterat är:
Lagringsfirmware. Den propritära mjukvara vi litar på i bl a hårddiskar och SSD. GPU, grafikkorts firmware / BIOS?

