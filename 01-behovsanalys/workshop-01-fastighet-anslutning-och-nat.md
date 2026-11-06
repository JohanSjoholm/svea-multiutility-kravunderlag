# Workshop 1: Fastighet, Anslutning, Nät & Leveranspunkt

**Datum:** 2026-10-14  
**Längd:** 90 min  
**Deltagare:**
- **Karin Lind** – Verksamhetsarkitekt / Moderator (Projektledare Datalager)
- **Anders Berg** – GIS- & Nätinformationsspecialist (NIS / Lantmäteriintegration)
- **Maria Söder** – Anslutningsingenjör (El, Fjärrvärme, Fjärrkyla, VA & Stadsnät)
- **Erik Holm** – Nätchef Elnät & Mätområde

---

## Transkribering

**[00:01:12] Karin Lind (Moderator):**  
Välkomna! Idag ska vi reda ut hur den fysiska verkligheten hänger ihop hos oss på Svea Stadsenergi. Alltså allt från själva fastigheten och byggnaderna, via den fysiska anslutningen och nätet, fram till leveranspunkten och tjänstestället. Vi har ju fem nyttigheter i koncernen: El, Fjärrvärme, Fjärrkyla, Vatten och Stadsnät. Anders, om vi börjar från marken – vad är det vi faktiskt registrerar kring en fastighet?

**[00:02:05] Anders Berg (GIS/Fastighet):**  
Ja, i grunden har vi objektet **Fastighet**. Varje fastighet har vårt interna `Fastighet ID`, men den hänger ihop med officiella register. Vi får in **Fastighetsdata** från Lantmäteriet där vi har `Fastighetsdata ID`, `Fastighet ID`, `FNR` (fastighetsnyckel), `UUID` och `Kommunkod`. Det är jätteviktigt att ni tänker på att ungefär 10 % av fastigheterna i vår kundreskontra saknar matchning mot Lantmäteriets fastighetsdata – alltså en partiell relation (typ 0.1-täckning på vissa äldre poster), och i dagsläget är **Fastighets_uppgifterva** bara en tom platshållare från VA-sidan som vi håller på att fasa in.

**[00:03:40] Karin Lind:**  
Okej, och vad ligger direkt på själva **Fastighet** jämfört med hjälptabellerna runt omkring?

**[00:04:02] Anders Berg:**  
På **Fastighet** har vi `Fastighet ID`, men också referenser till `Byggnadstyp ID`, `Byggnadsanvändning ID` och `Byggnadsstatus ID`. Sen har vi `Fastighetsnummer`, en flagga för om den är `Inaktiv`, en `Beskrivning`, `Byggnad antal`, `Debiterbar yta`, `Byggnadsår`, `Totalt antal byggnader`, `Total yta`, `Antal hushåll` och `Antal samlokaliserade verksamheter`.
De där tre klassificeringarna slår mot egna referenslistor:
- **Byggnadstyp** (`Byggnadstyp ID`, `Byggnadstyp` – t.ex. Småhus, Flerbostadshus, Industri).
- **Byggnadsanvändning** (`Byggnadsanvändning ID`, `Byggnadens användning`).
- **Byggnadsstatus** (`Byggnadsstatus ID`, `Byggnadsstatus` – t.ex. Gällande, Avregistrerad).

**[00:05:30] Maria Söder (Anslutning):**  
Men vänta Anders, vi har ju också enskilda byggnader inne på en fastighet! En industrifastighet eller bostadsrättsförening kan ha åtta hus.

**[00:05:45] Anders Berg:**  
Exakt! Därför har vi entiteten **Byggnad** (`Byggnad ID`, `Byggnadens namn`, `Byggnadsår`) och under den **Byggnadsdetaljer** (`Byggnadsdetaljer ID`, `Byggnad ID`, `Byggnadstyp`, `Antal`). Båda hänger ihop med **Fastighet**.  
Sen vill energirådgivningen och fjärrvärmesäljarna alltid veta hur fastigheten värms upp. En fastighet kan ha *flera* värmekällor – t.ex. fjärrvärme plus en gammal oljepanna eller solfångare. Så vi har en katalog med **Värmekälla** (`Värmekälla ID`, `Värmekälla`) och en kopplingstabell/brygga **Fastighet_Värmekälla** (`Fastighet_Värmekälla Id`, `Fastighet ID`, `Värmekälla ID`).

**[00:07:10] Karin Lind:**  
Hur vet vi vem som äger fastigheten över tid? Och vilken adress den har?

**[00:07:25] Anders Berg:**  
Ägandet ändras ju vid försäljning, så vi lagrar **Fastighetsägare_historik**. Den har `Fastighetsägare_historik ID`, `Fastighet ID`, `Person_Roll ID`, en `Aktiv`-flagga, `Startdatum` och `Slutdatum` – obs att start- och slutdatum där lagras som datum med klockslag i lokal tid! – samt en flagga `Tillåt interimsavtal`. Själva personen/rollen ligger i **Person_Roll** (`PersonID`, `Roll`).  
Och gällande adresser så har fastigheten en koppling till **Adress_historik** (`Entitet Adress ID`). Vi använder faktiskt **Adress_historik** på tre ställen i landskapet: på *Fastighet*, på *Anslutning* (där med både `Entitet Adress ID` och `Entitet ID`) och på *Tjänsteställe*.

**[00:09:00] Karin Lind:**  
Snyggt! Maria, om vi nu drar in kablar och rör till fastigheten – hur modellerar ni själva **Anslutning**? Och hur hänger fastigheten ihop med anslutningen?

**[00:09:20] Maria Söder:**  
Relationen mellan fastighet och anslutning är faktiskt tidsberoende vid avstyckningar och ombyggnationer, så i fastighetsdomänen finns en koppling **Anslutning_Fastighet_historik** som länkar `Fastighet` och `Anslutning`.
Själva **Anslutning** är vår generella entitet för en fysisk inkoppling. På huvudnivån **Anslutning** har vi:
- `Anslutning ID`
- `Fastighet ID`
- `Anslutningsstatus ID` (som pekar på **Anslutningsstatus** med `Anslutningsstatus ID` och `Status`, t.ex. Aktiv, Frånkopplad, Projekterad)
- `Anslutningsnummer`
- `Beskrivning`
- `Nät leveranspunkt 1` och `Nät leveranspunkt 2` (för koppling mot det fysiska nätet, t.ex. vid reservmatning).

**[00:11:05] Karin Lind:**  
Men en elanslutning har väl helt andra tekniska egenskaper än en fjärrvärme- eller fiberanslutning?

**[00:11:18] Maria Söder:**  
Ja, precis! Vi tittar egentligen på **Anslutning** i någon större utsträckning främst när det är en elanslutning, men arkitekturellt har vi en generell **Anslutning** och sedan fem specialiseringar beroende på nyttighet:
1. **Anslutning_El**: Den har `Anslutning ID` och är kopplad till **Säkring** (mätarsäkring/anslutningssäkring) och **Volt** (spänningsnivå).
2. **Anslutning_Fjärrvärme**
3. **Anslutning_Fjärrkyla**
4. **Anslutning_Vatten**
5. **Anslutning_Stadsnät**

**[00:12:45] Erik Holm (Nätchef):**  
Och glöm inte hur **Anslutning** hänger ihop med **Nät** och **Leveranspunkt**! I nätkartan har vi komponenten **Nät** som exponerar *Nät leveranspunkter* (vilket matchar `Nät leveranspunkt 1` och `Nät leveranspunkt 2` på Anslutning, samt matar in i Leveranspunkt).  
Men för mät- och avräkningssidan är det **Leveranspunkt** och **Nätområde** som är navet.

**[00:13:30] Karin Lind:**  
Berätta mer om **Leveranspunkt** och **Nätområde**, Erik! Vad är skillnaden på en Anslutning, en Leveranspunkt och ett Tjänsteställe? Det här brukar ju alltid röra till det i våra rapporter.

**[00:13:55] Erik Holm:**  
Haha, ja! Så här är det:
- **Anslutning** är den fysiska servisen/inkopplingen i gatan in till fastigheten.
- **Leveranspunkt** är den tekniska punkten där leveransen överlämnas och mäts. På **Leveranspunkt** har vi:
  - `Leveranspunkt Id`
  - `Anläggning ID` (som pekar på **Anläggning** med `Anläggning ID`)
  - `Anslutning ID` (kopplingen ner till Anslutning)
  - `Nätområde Id`
  - `Distrikt ID`
  - `Avläsningsbok ID` (pekar på **Avläsningsbok** med `Avläsningsbok Id` – används för ruttindelning vid manuell/radiobaserad avläsning)
  - `Mätarplacering`
  - `Plint`
  - `Antal faser`
- Till en leveranspunkt hör också **Leveranspunkt_noteringar** (`Leveranspunkt Id`, `Kontaktinfo`, `Mätarplaceringstext`, `Notering`, `Avl.notering`) så att fältteknikern vet om det sitter en arg hund på gården eller var nyckelskåpet är.
- Och **Nätområde** (`Nätområde ID`) är det koncessionerade eller tekniska nätområdet som leveranspunkten tillhör. Eftersom nätägare eller aktörer i ett nätområde kan ändras över tid har vi **Nätområde_aktör** (`Nätområde ID`, `Startdatum`, `Slutdatum`, `Nätägare`).

**[00:16:20] Karin Lind:**  
Och vad är då **Tjänsteställe** i relation till Fastighet och Leveranspunkt?

**[00:16:35] Maria Söder:**  
**Tjänsteställe** är platsen/objektet där en tjänst nyttjas (Useplace), t.ex. en specifik lokal, lägenhet eller abonnemangspunkt på fastigheten. Ett **Tjänsteställe** har:
- `Tjänsteställe ID`
- `Fastighet ID` (kopplat till Fastighet)
- `Värmemetod ID` (kopplat till **Värmemetod** med `Värmemetod ID` och `Kategori`)
- `Tjänsteställenummer` och `Alt tjänsteställenr`
- `Beskrivning`
- `Inaktivt` (flagga)
- `Permanent` (flagga)
- `Skyddsobjekt` (viktigt för samhällskritiska kunder!)
- `EDI-förbrukningsställe`
- `Objektnummer`
- `Antal lägenheter`
- `Yta`
Och som Anders nämnde har även **Tjänsteställe** en koppling till **Adress_historik** (`Entitet Adress ID`). Från Tjänsteställe och Leveranspunkt kliver vi sedan rakt in i Tjänst- och Mätpunktsvärlden!
