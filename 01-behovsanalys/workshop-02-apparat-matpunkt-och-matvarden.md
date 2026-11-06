# Workshop 2: Apparat, Mätpunkt & Mätvärde

**Datum:** 2026-10-16  
**Längd:** 90 min  
**Deltagare:**
- **Karin Lind** – Verksamhetsarkitekt / Moderator
- **Johan Ek** – Ansvarig Mätvärdesinsamling & MDM (Meter Data Management)
- **Sara Nyström** – Gruppchef Fältservice & Mätarverkstad
- **Peter Åkesson** – Analytiker Avräkning & Validering

---

## Transkribering

**[00:00:45] Karin Lind (Moderator):**  
Välkomna till workshop två! I förra workshopen slutade vi vid **Leveranspunkt**. Nu ska vi bena ut hur mätarna – eller apparaterna som ni kallar dem – sitter monterade, hur mätpunkter fungerar och hur mätvärdena flödar in. Sara, vad är egentligen en **Apparat** hos oss?

**[00:01:20] Sara Nyström (Fältservice):**  
I vår mätarverkstad kallar vi alla fysiska mätare och kommunikationsmoduler för **Apparat** (Device). En **Apparat** har:
- `Apparat ID` (vår unika systemnyckel)
- `Apparatnummer` (serienumret/individnumret som står tryckt på kapslingen)
- `Typ`
- `Model`
- `Status`

Men en apparat har ganska mycket metadata runt sig för livscykel och besiktningsintervall:
1. Den har en **Apparatstatus** (`Apparatstatus` / Device status – t.ex. I lager, Monterad, På revision, Skrotad).
2. Den tillhör ett **Apparat_inköpsparti** (`Apparat_inköpsparti` / Device purchase batch) så vi kan följa upp garantiärenden och revisionspartier enligt STAFS/Swedac.
3. Den har en **Apparat_tillverkare** (Device manufacturer, t.ex. Kamstrup, Landis+Gyr, Aidon).
4. Den har en **Apparattypbeteckning** (Device type description) och tillhör en **Apparattyp** (`Apparattyp ID` / Device type).
5. Eftersom en viss apparattyp kan mäta en eller flera nyttigheter (t.ex. El, Fjärrvärme eller Vatten) har vi en bryggtabell **Apparattyp_nyttighet** (`Apparattyp ID`, `Nyttighet ID` – Bridge Device type Utility).

**[00:03:50] Karin Lind:**  
Och när en tekniker åker ut och sätter upp eller byter en apparat ute på en leveranspunkt – hur spåras det?

**[00:04:05] Sara Nyström:**  
Då skapas en post i **Apparat_historik** (Fact Device delivery point). Där lagrar vi:
- `Apparat_historik ID`
- `Apparat ID`
- `Leveranspunkt ID` (som pekar direkt på **Leveranspunkt**!)
- `Startdatum`
- `Slutdatum`
På så sätt vet vi exakt vilken fysisk apparatindivid (`Apparat ID`) som har suttit på vilken `Leveranspunkt ID` under vilken tidsperiod.

**[00:05:10] Johan Ek (MDM):**  
Men här kommer en jätteviktig grej som alla BI-utvecklare brukar gå bet på! När vi kommer över till domänen **Mätpunkt** (Measurand) så har vi tabellen **Mätpunktsdetalj_historik** (Fact Measurand Device). Den har:
- `Mätpunkt ID`
- `Slutdatum`
- `Startdatum`
- `Apparatnummer`

Notera att den lagrar `Apparatnummer` och **inte** `Apparat ID`! Och i verkligheten är det så att **flera apparater ibland delar samma apparatnummer** (t.ex. underapparater/moduler eller dubblettserier från olika tillverkare). Därför är `Mätpunktsdetalj_historik` lite inexakt i att peka ut exakt *en* fysisk mätare om man bara går på `Apparatnummer` – man måste förstå relationen mellan `Apparat`, `Mätpunktsdetalj_historik` och `Mätpunkt`.

**[00:06:55] Karin Lind:**  
Tack Johan, guld värt att få med den detaljen! Förklara själva **Mätpunkt** – vad är skillnaden på en Leveranspunkt och en Mätpunkt?

**[00:07:15] Johan Ek:**  
En **Leveranspunkt** är den fysiska platsen där mätaren sitter, men på en och samma leveranspunkt (och i samma apparat) mäter vi ju flera olika storheter – t.ex. Aktiv Energi Uttag (kWh), Aktiv Energi Produktion, Effekt (kW), Flöde ($m^3$), Framledningstemperatur och Returtemperatur. Varje sådan mätstorhet/kanal är en **Mätpunkt**!  
På **Mätpunkt** har vi:
- `Mätpunkt ID`
- `Apparattyp ID` (pekar på **Apparattyp**)
- `Mättyp ID` (pekar på **Mättyp** i mätvärdesdomänen)
- `Enhet ID` och `Enhetstyp ID`

Och när en mätpunkt levererar en **Tjänst** till kunden kopplas `Mätpunkt` direkt mot `Tjänst`.

**[00:08:40] Peter Åkesson (Avräkning & Validering):**  
Låt oss prata om enheterna och mätvärdena också! Runt **Mätpunkt** och **Mätvärde** har vi en gemensam struktur för måttenheter:
- **Enhet** (Unit) med `Enhet ID`, `Enhet sv` (t.ex. kWh, MWh, m3, °C) och `Enhet en`.
- **Enhetstyp** (Unit type) med `Enhetstyp ID`, `Enhetstyp sv` (t.ex. Energi, Effekt, Volym, Temperatur) och `Enhetstyp en`.
Enhet är kopplad till både **Mätpunkt**, **Mättyp** och **Mätvärde** (där vi skiljer på mätpunktens enhet och mätvärdets rapporterade enhet).

**[00:10:05] Karin Lind:**  
Hur ser själva **Mätvärde**-domänen ut? Vi har väl både tidsserier (tim-/kvartsvärden), mätarställningar och årsförbrukning?

**[00:10:22] Peter Åkesson:**  
Precis! Vi har tre centrala faktaliknande begrepp inne i Mätvärde-domänen:
1. **Mätvärde** (Fact Meter readings): Här ligger själva tidsserievärdena (förbrukning per tidsintervall). Den innehåller:
   - `Mätpunkt ID`
   - `Datum tid`
   - `Status ID`
   - `Värde`
   - `Mätarställning`
   Den är kopplad till **Mättyp** (`Mättyp ID`, `Mättyp` / Measure type), **Enhet** och **Mätstatus** (`Mätstatus ID`, `Status` / Meter reading status).
2. **Mätarställning** (Fact Validated meter readings): Själva registerställningen på räkneverket (`Mätarställning Id`), kopplad till `Mätpunkt` och till en hel uppsättning validerings- och avläsningsattribut:
   - **Fas**
   - **Avläsningsorsak** (t.ex. Kundflytt, Periodisk, Mätarbyte)
   - **Avläsningstyp** (t.ex. Fjärravläst, Manuell, Uppskattad)
   - **Bruksdatavalidering**
   - **Rådatavalidering**
   - **KIS-status** (status för överföring till kundinformationssystemet/fakturering)
   - **Periodiseringsstatus**
3. **Årsförbrukning** (Fact Measurand annual consumption latest): Den beräknade årsförbrukningen (BFUS/beräknad årsenergi eller effekt) kopplad till `Mätpunkt` (för både Energi och Effekt), samt till **Periodschema** (Period scheme) och **Periodtyp** (Period).

**[00:13:40] Karin Lind:**  
Kristallklart! Då har vi hela kedjan från fysisk apparat via mätpunkt till validerade mätvärden och årsförbrukning.
