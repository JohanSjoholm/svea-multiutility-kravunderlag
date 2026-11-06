# Källsystem: Datautdrag och Tabellkatalog

Nedan redovisas de tabeller/entiteter och fält som identifierats vid källsystemsinventeringen, grupperade efter bolagets åtta huvuddomäner:
1. **APPARAT**
2. **MÄTPUNKT**
3. **MÄTVÄRDE**
4. **TJÄNST** (inkl. Avtal, Kund, Flextjänst, Utförande)
5. **ANLÄGGNING / NÄTOMRÅDE / NÄT**
6. **LEVERANSPUNKT / ANSLUTNING**
7. **FASTIGHET**
8. **TJÄNSTESTÄLLE**

---

## 1. Domän: APPARAT (Källa: MÄT-REG)

| Källtabell / Entitet | Engelskt systemnamn / Roll | Fält / Attribut | Kommentar |
| :--- | :--- | :--- | :--- |
| `APPARAT` | Device | `Apparat ID`, `Apparatnummer`, `Typ`, `Model`, `Status` | Fysisk mätare/enhet |
| `APPARATSTATUS` | Device status | *(Statusattribut)* | Kopplad till `APPARAT` |
| `APPARAT_INKÖPSPARTI` | Device purchase batch | *(Inköpspartiattribut)* | Kopplad till `APPARAT` |
| `APPARAT_TILLVERKARE` | Device manufacturer | *(Tillverkarattribut)* | Kopplad till `APPARAT` |
| `APPARATTYPBETECKNING` | Device type description | *(Beteckningsattribut)* | Kopplad till `APPARAT` |
| `APPARATTYP` | Device type | `Apparattyp ID` | Kopplad till `APPARAT`, `MÄTPUNKT` och `APPARATTYP_NYTTIGHET` |
| `APPARATTYP_NYTTIGHET` | Bridge Device type Utility | `Apparattyp ID`, `Nyttighet ID` | Bryggtabell mellan apparattyp och nyttighet |
| `APPARAT_HISTORIK` | Fact Device delivery point | `Apparat_historik ID`, `Apparat ID`, `Leveranspunkt ID`, `Startdatum`, `Slutdatum` | Tidsstyrd montering av apparat på leveranspunkt |

---

## 2. Domän: MÄTPUNKT (Källa: MDM)

| Källtabell / Entitet | Engelskt systemnamn / Roll | Fält / Attribut | Kommentar |
| :--- | :--- | :--- | :--- |
| `MÄTPUNKT` | Measurand | `Mätpunkt ID`, `Apparattyp ID`, `Mättyp ID`, `Enhet ID`, `Enhetstyp ID` | Mätkanal/mätstorhet; kopplad till `TJÄNST`, `MÄTVÄRDE`, `MÄTARSTÄLLNING`, `ÅRSFÖRBRUKNING` |
| `MÄTPUNKTSDETALJ_HISTORIK` | Fact Measurand Device | `Mätpunkt ID`, `Slutdatum`, `Startdatum`, `Apparatnummer` | OBS: Flera apparater delar samma `Apparatnummer`. Därför inexakt i att peka ut exakt en mätare. |
| `ENHET` | Unit | `Enhet ID`, `Enhet sv`, `Enhet en` | Ligger i gränslandet Mätpunkt/Mätvärde. Kopplad till `MÄTPUNKT` (Enhet), `MÄTVÄRDE` (Enhet ut) och `MÄTTYP`. |
| `ENHETSTYP` | Unit type | `Enhetstyp ID`, `Enhetstyp sv`, `Enhetstyp en` | Kopplad till `ENHET` |

---

## 3. Domän: MÄTVÄRDE (Källa: MDM)

| Källtabell / Entitet | Engelskt systemnamn / Roll | Fält / Attribut | Kommentar |
| :--- | :--- | :--- | :--- |
| `MÄTVÄRDE` | Fact Meter readings | `Mätpunkt ID`, `Datum tid`, `Status ID`, `Värde`, `Mätarställning` | Tidsseriemätvärden |
| `MÄTTYP` | Measure type | `Mättyp ID`, `Mättyp` | Kopplad till `MÄTVÄRDE`, `MÄTPUNKT` och `ENHET` |
| `MÄTSTATUS` | Meter reading status | `Mätstatus ID`, `Status` | Kopplad till `MÄTVÄRDE` |
| `MÄTARSTÄLLNING` | Fact Validated meter readings | `Mätarställning Id` | Kopplad till `MÄTPUNKT` samt valideringsattribut nedan |
| `FAS` | - | *(Validerings-/avläsningsdimension)* | Kopplad till `MÄTARSTÄLLNING` |
| `AVLÄSNINGSGRSAK` / `AVLÄSNINGSORSAK` | - | *(Validerings-/avläsningsdimension)* | Kopplad till `MÄTARSTÄLLNING` |
| `AVLÄSNINGSTYP` | - | *(Validerings-/avläsningsdimension)* | Kopplad till `MÄTARSTÄLLNING` |
| `BRUKSDATAVALIDERING` | - | *(Validerings-/avläsningsdimension)* | Kopplad till `MÄTARSTÄLLNING` |
| `RÅDATAVALIDERING` | - | *(Validerings-/avläsningsdimension)* | Kopplad till `MÄTARSTÄLLNING` |
| `KIS-STATUS` | - | *(Validerings-/avläsningsdimension)* | Kopplad till `MÄTARSTÄLLNING` |
| `PERIODISERINGSSTATUS` | - | *(Validerings-/avläsningsdimension)* | Kopplad till `MÄTARSTÄLLNING` |
| `ÅRSFÖRBRUKNING` | Fact Measurand annual consumption latest | *(Årsförbrukningsfakta)* | Har två relationer från `MÄTPUNKT`: *Energi* och *Effekt* |
| `PERIODSCHEMA` | Period scheme | *(Periodschemaattribut)* | Kopplad till `ÅRSFÖRBRUKNING` och `PERIODTYP` |
| `PERIODTYP` | Period | *(Periodtypattribut)* | Kopplad till `PERIODSCHEMA` |

---

## 4. Domän: TJÄNST, AVTAL & KUND (Källa: KIS & FLEX-PORTAL)

| Källtabell / Entitet | Engelskt systemnamn / Roll | Fält / Attribut | Kommentar |
| :--- | :--- | :--- | :--- |
| `TJÄNST` | Service | `Tjänst ID`, `Tjänsteställe ID`, `Affärsenhet ID`, `Nyttighet ID`, `Förbrukningsmönster ID`, `Leveranspunkt ID`, `Tjänstenummer`, `Beskrivning`, `Information`, `Internt nät` | Huvudentitet för levererad tjänst |
| `TJÄNSTESTATUS_HISTORIK` | Fact Service status | `Tjänstestatus_historik ID`, `Tjänst ID`, `Tjänstestatus ID`, `Startdatum`, `Slutdatum` | Statushistorik för tjänst |
| `TJÄNSTESTATUS` | Delivery status | `Tjänstestatus ID`, `Status` | Kopplad till `TJÄNSTESTATUS_HISTORIK` |
| `FÖRBRUKNINGSMÖNSTER` | Value cons profile | `Förbrukningsmönster Id` | Kopplad till `TJÄNST` |
| `TJÄNST_EL` | - | `Tjänst_El Id` | Subtyp till `TJÄNST`; kopplad till `SÄKRING` och `VOLT` |
| `SÄKRING` (under Tjänst) | - | *(Säkringsstorlek)* | Kopplad till `TJÄNST_EL` |
| `VOLT` (under Tjänst) | - | *(Spänningsnivå)* | Kopplad till `TJÄNST_EL` |
| `TJÄNST_FJÄRRVÄRME` | - | `Tjänst_Fjärrvärme Id` | Subtyp till `TJÄNST`; kopplad till `TEMPERATURSTATION` |
| `TEMPERATURSTATION` | - | *(Väder-/temperaturstation)* | Kopplad till `TJÄNST_FJÄRRVÄRME` |
| `TJÄNST_FJÄRRKYLA` | - | `Tjänst_Fjärrkyla Id` | Subtyp till `TJÄNST`; kopplad till `NOMINELLT_FLÖDE_QN` |
| `NOMINELLT_FLÖDE_QN` | - | *(Nominellt flöde)* | Kopplad till `TJÄNST_FJÄRRKYLA` |
| `TJÄNST_VATTEN` | - | `Tjänst_Vatten Id` | Subtyp till `TJÄNST` |
| `TJÄNST_STADSNÄT` | - | `Tjänst_Stadsnät Id` | Subtyp till `TJÄNST`; kopplad till `NÄTVERKSHASTIGHET` och `TV_TJÄNST` |
| `NÄTVERKSHASTIGHET` | - | *(Hastighet)* | Kopplad till `TJÄNST_STADSNÄT` |
| `TV_TJÄNST` | - | *(TV-paket)* | Kopplad till `TJÄNST_STADSNÄT` |
| `UTFÖRANDE` | - | `Utförande Id` | Kopplad till `TJÄNST` |
| `TJÄNST_FLEXTJÄNST` | - | `Tjänst_Flextjänst Id`, `Startdatum`, `Slutdatum` | Tidsstyrd koppling mellan `TJÄNST` och `FLEXTJÄNST` |
| `FLEXTJÄNST` | - | `Flextjänst Id` | Flexibilitetsprodukt |
| `AVTAL` | - | `Avtal ID`, `Kund ID`, `Avtalstyp ID`, `Affärsenhet ID`, `Verksamhetsområde ID`, `Avtalsnr`, `Avtalsdatum` | Kopplad till `TJÄNST` och `KUND` |
| `KUND` | - | `Kund ID`, `Person ID`, `Kundtyp ID`, `Inaktiv` | Kopplad till `AVTAL` |

---

## 5. Domän: ANLÄGGNING, NÄTOMRÅDE & NÄT (Källa: NIS/GIS)

| Källtabell / Entitet | Engelskt systemnamn / Roll | Fält / Attribut | Kommentar |
| :--- | :--- | :--- | :--- |
| `ANLÄGGNING` | Facility | `Anläggning ID` | Kopplad till `LEVERANSPUNKT` |
| `NÄTOMRÅDE` | Net area | `Nätområde ID` | Kopplad till `LEVERANSPUNKT` och `NÄTOMRÅDE_AKTÖR` |
| `NÄTOMRÅDE_AKTÖR` | Fact Net area actors | `Nätområde ID`, `Startdatum`, `Slutdatum`, `Nätägare` | Historik över aktör/nätägare per nätområde |
| `AVLÄSNINGSBOK` | Reading book | `Avläsningsbok Id` | Kopplad till `LEVERANSPUNKT` |
| `NÄT` | - | `Nät leveranspunkter` | Fysisk nätkomponent kopplad mot `LEVERANSPUNKT` och `ANSLUTNING` |

---

## 6. Domän: LEVERANSPUNKT & ANSLUTNING (Källa: NIS/GIS & MÄT-REG)

| Källtabell / Entitet | Engelskt systemnamn / Roll | Fält / Attribut | Kommentar |
| :--- | :--- | :--- | :--- |
| `LEVERANSPUNKT` | Delivery point | `Leveranspunkt Id`, `Anläggning ID`, `Anslutning ID`, `Nätområde Id`, `Distrikt ID`, `Avläsningsbok ID`, `Mätarplacering`, `Plint`, `Antal faser` | Kopplad till `ANLÄGGNING`, `NÄTOMRÅDE`, `AVLÄSNINGSBOK`, `ANSLUTNING`, `NÄT`, `APPARAT_HISTORIK` och `TJÄNST` |
| `LEVERANSPUNKT_NOTERINGAR` | - | `Leveranspunkt Id`, `Kontaktinfo`, `Mätarplaceringstext`, `Notering`, `Avl.notering` | Noteringar kring leveranspunkten |
| `ANSLUTNING` | Connection | `Anslutning ID`, `Fastighet ID`, `Anslutningsstatus ID`, `Anslutningsnummer`, `Beskrivning`, `Nät leveranspunkt 1`, `Nät leveranspunkt 2` | Generell fysisk anslutning |
| `ANSLUTNINGSSTATUS` | Connection status | `Anslutningsstatus ID`, `Status` | Kopplad till `ANSLUTNING` |
| `ADRESS_HISTORIK` (under Anslutning) | Fact Entity address | `Entitet Adress ID`, `Entitet ID` | Kopplad till `ANSLUTNING` |
| `ANSLUTNING_EL` | - | `Anslutning ID` | Subtyp till `ANSLUTNING`; kopplad till `SÄKRING` och `VOLT` |
| `SÄKRING` (under Anslutning) | - | *(Säkring)* | Kopplad till `ANSLUTNING_EL` |
| `VOLT` (under Anslutning) | - | *(Spänning)* | Kopplad till `ANSLUTNING_EL` |
| `ANSLUTNING_FJÄRRVÄRME` | - | *(Subtypsattribut)* | Subtyp till `ANSLUTNING` |
| `ANSLUTNING_FJÄRRKYLA` | - | *(Subtypsattribut)* | Subtyp till `ANSLUTNING` |
| `ANSLUTNING_VATTEN` | - | *(Subtypsattribut)* | Subtyp till `ANSLUTNING` |
| `ANSLUTNING_STADSNÄT` | - | *(Subtypsattribut)* | Subtyp till `ANSLUTNING` |

---

## 7. Domän: FASTIGHET (Källa: FAST-LM & KIS)

| Källtabell / Entitet | Engelskt systemnamn / Roll | Fält / Attribut | Kommentar |
| :--- | :--- | :--- | :--- |
| `FASTIGHET` | Site | `Fastighet ID`, `Byggnadstyp ID`, `Byggnadsanvändning ID`, `Byggnadsstatus ID`, `Fastighetsnummer`, `Inaktiv`, `Beskrivning`, `Byggnad antal`, `Debiterbar yta`, `Byggnadsår`, `Totalt antal byggnader`, `Total yta`, `Antal hushåll`, `Antal samlokaliserade verksamheter` | Central fastighetsentitet |
| `ANSLUTNING_FASTIGHET_HISTORIK` | - | *(Historikkoppling)* | Kopplar `FASTIGHET` och `ANSLUTNING` |
| `FASTIGHETSDATA` | Site estate code | `Fastighetsdata ID`, `Fastighet ID`, `FNR`, `UUID`, `Kommunkod` | Kopplad till `FASTIGHET` (med notering `0.1` om täckning/kardinalitet) |
| `FASTIGHET_UPPGIFTERVA` | - | *(VA-specifika fastighetsuppgifter)* | Platshållare i fastighetsdomänen |
| `BYGGNAD` | Building | `Byggnad ID`, `Byggnadens namn`, `Byggnadsår` | Kopplad till `FASTIGHET` och `BYGGNADSDETALJER` |
| `BYGGNADSDETALJER` | BuildingUsage | `Byggnadsdetaljer ID`, `Byggnad ID`, `Byggnadstyp`, `Antal` | Kopplad till `BYGGNAD` och `FASTIGHET` |
| `BYGGNADSTYP` | Building type | `Byggnadstyp ID`, `Byggnadstyp` | Kopplad till `FASTIGHET` |
| `BYGGNADSANVÄNDNING` | Use of building | `Byggnadsanvändning ID`, `Byggnadens användning` | Kopplad till `FASTIGHET` |
| `BYGGNADSSTATUS` | Code of building | `Byggnadsstatus ID`, `Byggnadsstatus` | Kopplad till `FASTIGHET` |
| `FASTIGHET_VÄRMEKÄLLA` | Bridge Site heating source | `Fastighet_Värmekälla Id`, `Fastighet ID`, `Värmekälla ID` | Brygga mellan `FASTIGHET` och `VÄRMEKÄLLA` |
| `VÄRMEKÄLLA` | Heating source | `Värmekälla ID`, `Värmekälla` | Kopplad till `FASTIGHET_VÄRMEKÄLLA` |
| `FASTIGHETSÄGARE_HISTORIK` | Fact site owner | `Fastighetsägare_historik ID`, `Fastighet ID`, `Person_Roll ID`, `Aktiv`, `Startdatum`, `Slutdatum`, `Tillåt interimsavtal` | Start-/Slutdatum är datum med klockslag i lokal tid |
| `PERSON_ROLL` | - | `PersonID`, `Roll` | Kopplad till `FASTIGHETSÄGARE_HISTORIK` |
| `ADRESS_HISTORIK` (under Fastighet) | Fact Entity address | `Entitet Adress ID` | Kopplad till `FASTIGHET` |

---

## 8. Domän: TJÄNSTESTÄLLE (Källa: KIS)

| Källtabell / Entitet | Engelskt systemnamn / Roll | Fält / Attribut | Kommentar |
| :--- | :--- | :--- | :--- |
| `TJÄNSTESTÄLLE` | Useplace | `Tjänsteställe ID`, `Fastighet ID`, `Värmemetod ID`, `Tjänsteställenummer`, `Alt tjänsteställenr`, `Beskrivning`, `Inaktivt`, `Permanent`, `Skyddsobjekt`, `EDI-förbrukningsställe`, `Objektnummer`, `Antal lägenheter`, `Yta` | Kopplad till `FASTIGHET`, `TJÄNST`, `VÄRMEMETOD` och `ADRESS_HISTORIK` |
| `VÄRMEMETOD` | Heating method | `Värmemetod ID`, `Kategori` | Kopplad till `TJÄNSTESTÄLLE` |
| `ADRESS_HISTORIK` (under Tjänsteställe) | Fact Entity address | `Entitet Adress ID` | Kopplad till `TJÄNSTESTÄLLE` |
