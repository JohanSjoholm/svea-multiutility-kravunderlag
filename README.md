# Svea Stadsenergi & Nät AB – Krav- och Arkitekturunderlag (Steg 1–3)

Detta repository innehåller **råmaterial och kravunderlag (Steg 1–3)** från ett svenskt kommunägt energibolag (**Svea Stadsenergi & Nät AB**) som bedriver multi-utility-verksamhet inom **El**, **Fjärrvärme**, **Fjärrkyla**, **Vatten & Avlopp (VA)** samt **Stadsnät (Fiber/TV)**, utöver flexibilitetstjänster.

Syftet med detta underlag är att utgöra indata till en AI-agent / Datamodellerings-Mate som ska automatisera:
- **Steg 4: Informationsmodell (Kanonisk datamodell)** – Verksamhetsbegrepp, relationer, supertyp/subtyp-mönster, historiktabeller och definitioner.
- **Steg 5: Semantisk datamodell** – Fakta- och dimensionsmodell (Star Schema / Data Marts) samt nyckeltalsdefinitioner för analys och BI.

> **OBS!** Detta repo innehåller medvetet **endast underlaget (Steg 1–3)** – inga färdiga ER-diagram eller kanoniska tabellmodeller finns här. Modelleringsagenten ska på egen hand analysera transkriberingarna, user stories, systemlandskapet och processkartorna för att härleda informationsmodellen och den semantiska modellen.

---

## 📂 Mappstruktur i repot

### `01-behovsanalys/`
Underlag från kravinsamling och intervjuer med verksamheten:
- `workshop-01-fastighet-anslutning-och-nat.md` – Transkribering av workshop med Fastighetsdata/GIS, Anslutningsingenjörer och Nätplanering (behandlar bl.a. Fastighet, Byggnad, Värmekälla, Anslutning per nyttighet, Leveranspunkt, Nätområde och Nät).
- `workshop-02-apparat-matpunkt-och-matvarden.md` – Transkribering av workshop med Mätinsamling, Fältservice och Validering (behandlar bl.a. Apparat, Inköpsparti, Apparattyp/Nyttighet, Mätpunkt, Mätpunktsdetalj-historik med delade apparatnummer, Mätvärde, Mätarställning, Årsförbrukning, Periodschema och Valideringsstatusar).
- `workshop-03-tjanst-avtal-och-kund.md` – Transkribering av workshop med Marknad/Kund, Produktägare och Fakturering (behandlar bl.a. Tjänsteställe, Tjänst och nyttighetsspecifika tjänster för El/Fjärrvärme/Fjärrkyla/Vatten/Stadsnät, Tjänstestatus-historik, Förbrukningsmönster, Flextjänst, Utförande, Avtal och Kund).
- `user-stories-backlog.md` – Komplett backlog med 24 User Stories och acceptanskriterier uppdelade per domänområde.
- `utdrag-verksamhetsregler-och-mejlslingor.md` – Kompletterande mejlslingor och anteckningar från verksamhetsexperter kring kända datakvalitetsproblem och historikkrav.

### `02-losningsarkitektur/`
Underlag kring källsystem, integrationer och fysiska datastrukturer:
- `systemlandskap-och-integrationer.md` – Översikt av bolagets systemlandskap (CIS/KIS, MDM/Mätvärdesdatabas, NIS/GIS, Fält- & Apparatregister, Fastighetsregister/Lantmäteriet, Stadsnätsplattform m.fl.) inklusive integrationsflöden i Mermaid.
- `kallsystem-datautdrag-och-falt.md` – Råa tabell- och fältutdrag (exempelpayloads och kolumnlistor) från källsystemen som visar vilka attribut som finns tillgängliga.

### `03-process-och-formagor/`
Verksamhetsarkitektur (Business Capabilities & Processer):
- `process-och-formagekarta.md` – Kartläggning av bolagets kärnprocesser (från Fastighets- & Anslutningsetablering till Mät- & Leveranskedja samt Kund- & Tjänsteleverans) och vilka affärsförmågor som stöds av vilka system.
