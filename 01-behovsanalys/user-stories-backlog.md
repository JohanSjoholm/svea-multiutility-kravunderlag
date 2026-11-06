# Backlog: User Stories för Datalager & Informationsmodell

Nedan listas prioriterade User Stories från verksamheten (Elnät, Fjärrvärme/Kyla, VA, Stadsnät, Mätinsamling och Marknad/Kund). Dessa utgör kravbasen för vad informationsmodellen (Steg 4) och den semantiska datamodellen (Steg 5) måste stödja.

---

## Epic 1: Fastighet, Byggnad & Ägarstruktur

### US-01: Sammanhållen fastighets- och byggnadsinformation
**Som** energi- och nätplanerare  
**Vill jag** kunna analysera fastigheter tillsammans med deras byggnader, byggnadsdetaljer, byggnadstyp, byggnadsanvändning och byggnadsstatus  
**Så att** vi kan dimensionera nät och värmeleveranser baserat på debiterbar yta, antal hushåll och samlokaliserade verksamheter.  
**Acceptanskriterier:**
- Stöd för både `Fastighet` (med attribut som `Fastighetsnummer`, `Debiterbar yta`, `Byggnadsår`, `Totalt antal byggnader`, `Total yta`, `Antal hushåll`, `Antal samlokaliserade verksamheter`) och underliggande `Byggnad` samt `Byggnadsdetaljer`.
- Koppling till officiell `Fastighetsdata` (`FNR`, `UUID`, `Kommunkod`) även när matchningsgraden bara är partiell (ca 10 % / 0.1 saknar matchning eller har delmatchning).
- Platshållare för `Fastighets_uppgifterva` ska finnas identifierad i fastighetsdomänen.

### US-02: Kartläggning av fastigheters värmekällor
**Som** affärsutvecklare inom Fjärrvärme  
**Vill jag** se samtliga registrerade värmekällor per fastighet  
**Så att** vi kan identifiera konverteringspotential och kombinationsuppvärmning.  
**Acceptanskriterier:**
- Många-till-många-relation mellan `Fastighet` och `Värmekälla` via bryggan `Fastighet_Värmekälla` (`Fastighet_Värmekälla Id`, `Fastighet ID`, `Värmekälla ID`).

### US-03: Historisk spårbarhet av fastighetsägare och adresser
**Som** utredare på kund- och avtalsavdelningen  
**Vill jag** kunna se vem som ägt en fastighet vid en viss tidpunkt (med datum och klockslag i lokal tid) och om interimsavtal varit tillåtet  
**Så att** vi kan reda ut betalningsansvar vid flytt och ägarbyten.  
**Acceptanskriterier:**
- `Fastighetsägare_historik` kopplar `Fastighet` till `Person_Roll` (`PersonID`, `Roll`) med `Startdatum`, `Slutdatum`, `Aktiv` och `Tillåt interimsavtal`.
- `Adress_historik` (`Entitet Adress ID`) ska kunna knytas till `Fastighet`, `Anslutning` och `Tjänsteställe`.

---

## Epic 2: Anslutning, Nät & Leveranspunkt

### US-04: Hantering av fysiska anslutningar per nyttighet
**Som** anslutningsingenjör  
**Vill jag** kunna följa en fysisk anslutning, dess status och dess nyttighetsspecifika egenskaper  
**Så att** vi har kontroll på våra serviser för El, Fjärrvärme, Fjärrkyla, Vatten och Stadsnät.  
**Acceptanskriterier:**
- Generell entitet `Anslutning` (`Anslutning ID`, `Fastighet ID`, `Anslutningsstatus ID`, `Anslutningsnummer`, `Beskrivning`, `Nät leveranspunkt 1`, `Nät leveranspunkt 2`) kopplad till `Anslutningsstatus`.
- Historikkoppling mellan fastighet och anslutning via `Anslutning_Fastighet_historik`.
- Stöd för 5 subtyper: `Anslutning_El` (med koppling till `Säkring` och `Volt`), `Anslutning_Fjärrvärme`, `Anslutning_Fjärrkyla`, `Anslutning_Vatten` och `Anslutning_Stadsnät`.

### US-05: Koppling mellan Nät, Nätområde, Anläggning och Leveranspunkt
**Som** nätchef  
**Vill jag** kunna spåra varje leveranspunkt till rätt nätområde (inkl. historisk nätägare), anläggning, avläsningsbok och fysiska nätpunkter  
**Så att** nätavräkning och fältarbete fungerar sömlöst.  
**Acceptanskriterier:**
- `Leveranspunkt` innehåller `Leveranspunkt Id`, `Anläggning ID`, `Anslutning ID`, `Nätområde Id`, `Distrikt ID`, `Avläsningsbok ID`, `Mätarplacering`, `Plint`, `Antal faser`.
- `Leveranspunkt_noteringar` lagrar fältnoteringar (`Kontaktinfo`, `Mätarplaceringstext`, `Notering`, `Avl.notering`).
- `Nätområde` kopplas till `Nätområde_aktör` (`Nätområde ID`, `Startdatum`, `Slutdatum`, `Nätägare`).
- Koppling finns mot `Anläggning` (`Anläggning ID`), `Avläsningsbok` (`Avläsningsbok Id`) samt `Nät` (`Nät leveranspunkter`).

---

## Epic 3: Apparat (Mätarindivid) & Mätpunkt

### US-06: Livscykel- och partispårning av apparater
**Som** ansvarig för mätarverkstaden  
**Vill jag** kunna följa varje apparats status, tillverkare, inköpsparti, apparattyp och vilka nyttigheter apparattypen stödjer  
**Så att** vi uppfyller myndighetskrav (Swedac/STAFS) för mätarrevision.  
**Acceptanskriterier:**
- `Apparat` (`Apparat ID`, `Apparatnummer`, `Typ`, `Model`, `Status`) kopplad till `Apparatstatus`, `Apparat_inköpsparti`, `Apparat_tillverkare`, `Apparattypbeteckning` och `Apparattyp`.
- Brygga `Apparattyp_nyttighet` (`Apparattyp ID`, `Nyttighet ID`) som kopplar apparattyp till nyttighet.

### US-07: Spårning av apparatmontering på leveranspunkt respektive mätpunkt
**Som** mätdataanalytiker  
**Vill jag** kunna se både vilken fysisk apparat (`Apparat ID`) som suttit på en leveranspunkt över tid OCH vilket `Apparatnummer` som varit registrerat på en mätpunkt över tid  
**Så att** vi kan felsöka mätvärden även när flera apparater delar samma apparatnummer.  
**Acceptanskriterier:**
- `Apparat_historik` kopplar `Apparat ID` och `Leveranspunkt ID` med `Startdatum` och `Slutdatum`.
- `Mätpunktsdetalj_historik` kopplar `Mätpunkt ID` och `Apparatnummer` med `Startdatum` och `Slutdatum` (med dokumenterad affärsregel att `Apparatnummer` inte är globalt unikt per fysisk apparat).
- `Mätpunkt` innehåller `Mätpunkt ID`, `Apparattyp ID`, `Mättyp ID`, `Enhet ID` och `Enhetstyp ID`.

---

## Epic 4: Mätvärde, Mätarställning & Årsförbrukning

### US-08: Analys av tidsseriemätvärden med enhet och status
**Som** analytiker på avräkningsavdelningen  
**Vill jag** kunna analysera mätvärden per mätpunkt och tidpunkt med tillhörande mättyp, enhet och mätstatus  
**Så att** vi kan särskilja energi- och effektvärden samt filtrera bort osäkra mätvärden.  
**Acceptanskriterier:**
- `Mätvärde` (`Mätpunkt ID`, `Datum tid`, `Status ID`, `Värde`, `Mätarställning`) kopplat till `Mättyp`, `Mätstatus` och `Enhet` (som i sin tur tillhör `Enhetstyp`).

### US-09: Uppföljning av validerade mätarställningar och valideringsflaggor
**Som** faktureringsansvarig  
**Vill jag** kunna se mätarställningar och deras valideringsstatusar (rådata, bruksdata, KIS-status, periodisering, avläsningsorsak, avläsningstyp, fas)  
**Så att** inga felaktiga mätarställningar går vidare till fakturering i KIS.  
**Acceptanskriterier:**
- `Mätarställning` (`Mätarställning Id`) kopplad till `Mätpunkt` samt dimensionerna/attributen `Fas`, `Avläsningsorsak`, `Avläsningstyp`, `Bruksdatavalidering`, `Rådatavalidering`, `KIS-status` och `Periodiseringsstatus`.

### US-10: Årsförbrukning per periodschema (Energi & Effekt)
**Som** nät- och handelsanalytiker  
**Vill jag** kunna hämta senaste gällande årsförbrukning (både för energi och effekt) per mätpunkt och periodschema  
**Så att** vi kan prognostisera volymer och rapportera schablon-/årsförbrukning.  
**Acceptanskriterier:**
- `Årsförbrukning` kopplad till `Mätpunkt` (via separata rollkopplingar för *Energi* respektive *Effekt*), `Periodschema` och `Periodtyp`.

---

## Epic 5: Tjänsteställe, Tjänst, Flextjänst, Avtal & Kund

### US-11: Uppföljning av tjänsteställen och skyddsobjekt
**Som** drift- och kundansvarig  
**Vill jag** kunna se alla tjänsteställen på en fastighet inklusive värmemetod, yta, antal lägenheter och om platsen är ett skyddsobjekt  
**Så att** vi kan prioritera samhällskritiska leveranser vid driftstörningar.  
**Acceptanskriterier:**
- `Tjänsteställe` innehåller `Tjänsteställe ID`, `Fastighet ID`, `Värmemetod ID`, `Tjänsteställenummer`, `Alt tjänsteställenr`, `Beskrivning`, `Inaktivt`, `Permanent`, `Skyddsobjekt`, `EDI-förbrukningsställe`, `Objektnummer`, `Antal lägenheter`, `Yta`, samt koppling till `Värmemetod` och `Adress_historik`.

### US-12: Helhetsbild av Tjänster per nyttighet, statushistorik och flextjänster
**Som** produktchef  
**Vill jag** kunna analysera våra tjänster både samlat och per nyttighet (El, Fjärrvärme, Fjärrkyla, Vatten, Stadsnät), följa tjänstestatus över tid samt se vilka tjänster som har aktiva flextjänster  
**Så att** vi kan följa upp produktportföljen och styrbara laster.  
**Acceptanskriterier:**
- Huvudentitet `Tjänst` (`Tjänst ID`, `Tjänsteställe ID`, `Affärsenhet ID`, `Nyttighet ID`, `Förbrukningsmönster ID`, `Leveranspunkt ID`, `Tjänstenummer`, `Beskrivning`, `Information`, `Internt nät`) kopplad till `Förbrukningsmönster`, `Utförande`, `Mätpunkt`, `Leveranspunkt` och `Tjänsteställe`.
- `Tjänstestatus_historik` (`Tjänstestatus_historik ID`, `Tjänst ID`, `Tjänstestatus ID`, `Startdatum`, `Slutdatum`) kopplad till `Tjänstestatus`.
- 5 nyttighetsspecifika subtyper:
  - `Tjänst_El` (kopplad till `Säkring` och `Volt`)
  - `Tjänst_Fjärrvärme` (kopplad till `Temperaturstation`)
  - `Tjänst_Fjärrkyla` (kopplad till `Nominellt_flöde_qn`)
  - `Tjänst_Vatten`
  - `Tjänst_Stadsnät` (kopplad till `Nätverkshastighet` och `TV_tjänst`)
- Koppling till `Flextjänst` via `Tjänst_Flextjänst` (`Tjänst_Flextjänst Id`, `Startdatum`, `Slutdatum`).

### US-13: Koppling mellan Tjänst, Avtal och Kund
**Som** controller  
**Vill jag** kunna koppla varje levererad tjänst till rätt avtal, affärsenhet, verksamhetsområde och kund  
**Så att** intäkter och volymer kan analyseras per kundsegment och bolag i koncernen.  
**Acceptanskriterier:**
- `Avtal` (`Avtal ID`, `Kund ID`, `Avtalstyp ID`, `Affärsenhet ID`, `Verksamhetsområde ID`, `Avtalsnr`, `Avtalsdatum`) kopplat till `Tjänst` och `Kund`.
- `Kund` (`Kund ID`, `Person ID`, `Kundtyp ID`, `Inaktiv`).
