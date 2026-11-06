# Utdrag: Verksamhetsregler, Domännoteringar & Mejlslingor

Detta dokument innehåller viktiga domänregler som arkitekter och verksamhetsexperter skickat in efter workshopsen.

---

## 📧 Mejl 1: Angående Apparatnummer vs Apparat ID på Mätpunkt
**Från:** Johan Ek (MDM-ansvarig)  
**Till:** Karin Lind (Verksamhetsarkitekt)  
**Ämne:** VIKTIGT om Mätpunktsdetalj_historik!

Hej Karin!  
Ville bara förtydliga det jag nämnde på workshopen så det kommer med i er modell:
I tabellen `Mätpunktsdetalj_historik` finns bara `Apparatnummer` (tillsammans med `Mätpunkt ID`, `Startdatum`, `Slutdatum`), inte `Apparat ID`. Eftersom flera fysiska apparater i `Apparat`-registret kan dela samma `Apparatnummer` är `Mätpunktsdetalj_historik` inexakt om man försöker peka ut en enskild mätarindivid enbart via den vägen. För exakt individspårning mot den fysiska platsen måste man även titta på `Apparat_historik` som kopplar `Apparat ID` direkt mot `Leveranspunkt ID`.

---

## 📧 Mejl 2: Angående Anslutning och nyttigheter
**Från:** Maria Söder (Anslutningsingenjör)  
**Till:** Karin Lind (Verksamhetsarkitekt)  
**Ämne:** Subtyper under Anslutning och Säkring/Volt

Hej!  
Två saker att tänka på:
1. I praktiken tittar vi på `Anslutning` i någon större utsträckning främst när det gäller en elanslutning (`Anslutning_El`), men modellen ska ha med alla fem nyttigheterna (`Anslutning_El`, `Anslutning_Fjärrvärme`, `Anslutning_Fjärrkyla`, `Anslutning_Vatten`, `Anslutning_Stadsnät`).
2. Lägg märke till att referensbegreppen `Säkring` och `Volt` används på **två** ställen: dels tekniskt på `Anslutning_El` (vad servisen klarar), och dels kommersiellt på `Tjänst_El` (vilken säkring/spänning kunden abonnerar på).

---

## 📧 Mejl 3: Tidsstämplar och Fastighetsdata
**Från:** Anders Berg (GIS)  
**Till:** Karin Lind (Verksamhetsarkitekt)  
**Ämne:** Fastighetsägare_historik och Fastighetsdata

Hej Karin,  
Notera i begreppsmodellen att `Startdatum` och `Slutdatum` i `Fastighetsägare_historik` inte bara är datum utan **datum med klockslag i lokal tid** (eftersom ett tillträde/ägarbyte kan ske mitt på dagen).  
Glöm inte heller att `Enhet` har två olika kopplingar mot mätflödet: en koppling till `Mätpunkt` (och `Mättyp`), och en direktkoppling till `Mätvärde`.
