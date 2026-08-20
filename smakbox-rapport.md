# Rapport Smakbox

**Grupp:**  
**Deltagare:** Kevin Hermansson, Josef Al-Husseini  
**Datum:** 2026-08-19

---

## 1. Valda Azure-tjänster

| Krav | Tjänst | Plan / konfiguration | Motivering |
|---|---|---|---|
| Webbapp | Azure App Service | Standard S1, Linux, Sweden Central | En enkel managed lösning för webbappen. Standard-planen passar produktion och gör det möjligt att skala ut vid högre belastning. |
| Fillagring | Azure Blob Storage | Hot, ZRS, 50 GB | Passar bra för receptbilder och PDF-filer som används ofta. ZRS ger bättre redundans genom att lagra data över flera zoner i samma region. |
| Databas | Azure SQL Database | General Purpose, Serverless, 0,5–2 vCores, 32 GB | Managed relationsdatabas som kan anpassa beräkningskapaciteten efter belastningen. Serverless passar eftersom trafiken varierar. |
| Skalbarhet | App Service Autoscale | 1 instans normalt, upp till 3 vid fredagstopp | Smakbox har en tydlig trafikpeak på fredagar. Lösningen kan därför skala ut automatiskt utan manuellt arbete. |

---

## 2. AWS-motsvarigheter

| Azure-tjänst | AWS-tjänst | Noteringar |
|---|---|---|
| Azure App Service | AWS Elastic Beanstalk + EC2 | Elastic Beanstalk används för att hantera webbappen och EC2 står för beräkningskapaciteten. |
| Azure Blob Storage | Amazon S3 | Objektlagring för receptbilder och PDF-filer. |
| Azure SQL Database | Amazon RDS for PostgreSQL | Managed relationsdatabas för kunder, ordrar och prenumerationer. |
| App Service Autoscale | EC2 Auto Scaling + Application Load Balancer | Kan öka antalet EC2-instanser när belastningen stiger. |

---

## 3. Kostnadsjämförelse

### Antaganden

- All data placeras i Sverige/EU.
- 50 GB lagring för receptbilder och PDF-filer.
- 100 000 läsningar och 10 000 skrivningar per månad.
- Webbappen kör 1 instans normalt.
- Under fredagstoppen kan webbappen skala upp till 3 instanser under cirka 4 timmar per vecka.
- Azure SQL Serverless antas vara aktiv cirka 200 timmar per månad.
- AWS RDS körs hela månaden som en Single-AZ-instans.
- AWS använder EC2 `t3.small` och RDS PostgreSQL `db.t4g.medium`.
- Kostnaderna är beräknade med Azure Pricing Calculator och AWS Pricing Calculator.
- AWS-priset på 87,96 USD motsvarade cirka 75,80 € vid beräkningstillfället.

| Del | Azure (€/mån) | AWS (ca €/mån) |
|---|---:|---:|
| Webbapp + skalning | 63,60 € | 30,53 € |
| Fillagring | 1,16 € | 1,07 € |
| Databas | 105,86 € | 44,20 € |
| **Totalt** | **ca 170,62 €** | **ca 75,80 €** |

### Underlag från priskalkylatorerna

**Kostnadsunderlag:** Fullständiga exporter från Azure Pricing Calculator och AWS Pricing Calculator bifogas separat i projektet.

### Azure

- App Service Standard S1: 60,93 €/månad.
- Blob Storage, 50 GB Hot med ZRS: cirka 1,16 €/månad.
- Azure SQL Database Serverless: 105,86 €/månad.
- Kalkylatorns grundsumma: 167,95 €/månad.
- Vi har lagt till cirka 2,67 €/månad för extra App Service-instanser under fredagstoppen.
- Beräknad totalsumma: cirka 170,62 €/månad.

### AWS

- Amazon EC2 + EBS: 17,40 USD/månad.
- Application Load Balancer: 18,03 USD/månad.
- Amazon S3: 1,24 USD/månad.
- Amazon RDS for PostgreSQL: 51,29 USD/månad.
- Totalt: 87,96 USD/månad, cirka 75,80 € vid beräkningstillfället.

---

## 4. Rekommendation

Vi rekommenderar **Azure** för Smakbox AB.

AWS blev billigare i vår rena kostnadskalkyl, men vi anser ändå att Azure passar Smakbox bättre som helhetslösning.

De viktigaste anledningarna är:

1. **Mindre driftsansvar.** Azure App Service och Azure SQL Database ger en tydlig PaaS-lösning där Smakbox slipper hantera underliggande servrar och flera separata infrastrukturkomponenter.

2. **Azure SQL Serverless passar trafikmönstret.** Databasen kan skala beräkningskapaciteten efter belastningen och kan auto-pausa under perioder utan aktivitet. Det gör lösningen lämplig för Smakbox där belastningen varierar under veckan.

3. **Enklare arkitektur.** Azure-lösningen består främst av App Service, Blob Storage och Azure SQL. AWS-lösningen kräver fler separata delar, bland annat Elastic Beanstalk, EC2, Auto Scaling och en Application Load Balancer.

4. **Båda alternativen ligger under budget.** Azure beräknas kosta cirka 170,62 € per månad, vilket fortfarande ligger långt under Smakbox budget på 500 € per månad.

AWS är alltså billigare i vår kalkyl, men vi bedömer att Azures enklare drift, serverless-databas och tydliga PaaS-upplägg är värda den högre kostnaden för Smakbox.

---

## Bidragsdokumentation

| Namn | Uppgift |
|---|---|
| Kevin Hermansson | Azure-tjänster, Azure Pricing Calculator, AWS-tjänster, AWS Pricing Calculator och dokumentation |
| Josef Al-Husseini | Presentation |
| Båda | Jämförelse och rekommendation |
