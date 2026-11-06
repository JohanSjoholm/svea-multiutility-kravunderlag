# Workshop 3: Tjänst, Tjänsteställe, Avtal & Kund

**Datum:** 2026-10-20  
**Längd:** 90 min  
**Deltagare:**
- **Karin Lind** – Verksamhetsarkitekt / Moderator
- **Helena Väst** – Affärsutvecklare & Produktägare Multi-utility (El, Fjärrvärme, Kyla, VA, Stadsnät)
- **Magnus Käll** – Chef Kundservice & Fakturering (CIS/KIS)
- **Lina Fors** – Ansvarig Flexibilitetstjänster & Nya Energilösningar

---

## Transkribering

**[00:00:50] Karin Lind (Moderator):**  
Välkomna till den tredje och sista workshopen! Nu ska vi knyta ihop den tekniska infrastrukturen (Leveranspunkt, Mätpunkt och Tjänsteställe) med den kommersiella sidan: **Tjänst**, **Avtal** och **Kund**. Helena, vad är en **Tjänst** (Service) i vår begreppsvärld?

**[00:01:30] Helena Väst (Produktägare):**  
En **Tjänst** är själva leveransen av en nyttighet till kunden på en viss plats. Den fungerar som bryggan mellan tekniken och affären! På huvudobjektet **Tjänst** har vi:
- `Tjänst ID`
- `Tjänsteställe ID` (pekar ner på **Tjänsteställe**)
- `Affärsenhet ID` (vilket bolag i koncernen som levererar tjänsten)
- `Nyttighet ID` (El, Fjärrvärme, Fjärrkyla, Vatten eller Stadsnät)
- `Förbrukningsmönster ID` (pekar på **Förbrukningsmönster** / Value cons profile med `Förbrukningsmönster Id`)
- `Leveranspunkt ID` (pekar på **Leveranspunkt** – och som vi sa i förra workshopen är `Tjänst` även kopplad mot `Mätpunkt`)
- `Tjänstenummer`
- `Beskrivning`
- `Information`
- `Internt nät` (flagga om leveransen sker i ett internt nät)

**[00:03:45] Karin Lind:**  
Hur spårar vi om en tjänst är påkopplad, avstängd på grund av bristande betalning, eller vilande?

**[00:04:00] Magnus Käll (Kund & Fakturering):**  
Det måste vi kunna se historiskt per datum! Därför har vi **Tjänstestatus_historik** (Fact Service status) som innehåller:
- `Tjänstestatus_historik ID`
- `Tjänst ID`
- `Tjänstestatus ID`
- `Startdatum`
- `Slutdatum`
Den pekar på referenstabellen **Tjänstestatus** (Delivery status) med `Tjänstestatus ID` och `Status`.

**[00:05:10] Karin Lind:**  
Precis som på Anslutning såg jag i era systemskisser att **Tjänst** har fem olika grenar beroende på vilken nyttighet det gäller. Kan du gå igenom dem, Helena?

**[00:05:28] Helena Väst:**  
Absolut! Eftersom varje nyttighet har unika tekniska och kommersiella parametrar på tjänstenivå har vi delat upp **Tjänst** i fem undertyper (subtyper):
1. **Tjänst_El** (`Tjänst_El Id`): Den är kopplad till **Säkring** (abonnemangssäkring, t.ex. 16A, 25A, 63A) och **Volt** (spänningsnivå).
2. **Tjänst_Fjärrvärme** (`Tjänst_Fjärrvärme Id`): Den är kopplad till **Temperaturstation** (vilken väder-/temperaturstation som styr normalårskorrigering och effektberäkning).
3. **Tjänst_Fjärrkyla** (`Tjänst_Fjärrkyla Id`): Den är kopplad till **Nominellt_flöde_qn** (dimensionerande flöde $q_n$).
4. **Tjänst_Vatten** (`Tjänst_Vatten Id`): För VA-leveransen.
5. **Tjänst_Stadsnät** (`Tjänst_Stadsnät Id`): För bredband och media. Den är kopplad till **Nätverkshastighet** (t.ex. 100/100, 1000/1000 Mbit/s) och **TV_tjänst** (vilket TV-utbud som ingår).

**[00:07:35] Karin Lind:**  
Och vad är de där boxarna utanför tjänstedomänen som heter **Utförande** och **Flextjänst**, Lina?

**[00:07:50] Lina Fors (Flexibilitet & Nya tjänster):**  
Jo! Dels har vi **Utförande** (`Utförande Id`) som är kopplat till **Tjänst** och anger utförandeform/leveransutförande.  
Dels har vi våra nya smarta energitjänster – t.ex. effektstyrning av elbilsladdare, fjärrvärmecentraler eller batterilager. Det kallar vi **Flextjänst** (`Flextjänst Id`). Eftersom en kund kan slå på och av en flexibilitetstjänst på sin befintliga tjänst över tid, ligger det en kopplingstabell med datumintervall emellan som heter **Tjänst_Flextjänst** (`Tjänst_Flextjänst Id`, `Startdatum`, `Slutdatum`) som knyter ihop **Tjänst** med **Flextjänst**.

**[00:09:20] Karin Lind:**  
Sist men inte minst, Magnus: Hur ser kopplingen ut till **Avtal** och **Kund**?

**[00:09:35] Magnus Käll:**  
En **Tjänst** regleras kommersiellt av ett **Avtal**. På **Avtal** har vi:
- `Avtal ID`
- `Kund ID` (kopplat till **Kund**)
- `Avtalstyp ID`
- `Affärsenhet ID`
- `Verksamhetsområde ID`
- `Avtalsnr`
- `Avtalsdatum`

Och **Kund** i sin tur är den juridiska eller fysiska motparten som tecknar avtalet. På **Kund** har vi:
- `Kund ID`
- `Person ID` (person- eller organisationskoppling)
- `Kundtyp ID` (t.ex. Privatkund, Företagskund, Offentlig förvaltning)
- `Inaktiv` (flagga)

Det fina när vi separerar **Fastighet** $\rightarrow$ **Anslutning** / **Tjänsteställe** $\rightarrow$ **Leveranspunkt** $\rightarrow$ **Tjänst** $\rightarrow$ **Avtal** $\rightarrow$ **Kund** är att en fastighetsägare (via `Fastighetsägare_historik`) kan vara en helt annan person än den hyresgäst eller verksamhet som står som **Kund** på **Avtal** för en specifik **Tjänst**!
