# Molnutvärdering & Rekommendation: Smakbox AB
**Skriven av:** Josef Alhusseini  
**Datum:** 19 Augusti 2026  
**Kurs:** Molnarkitektur & Infrastruktur  

---

## 1. Inledning och Bakgrund
Smakbox AB är ett svenskt prenumerationsföretag som levererar matlådor och recept till cirka 200 aktiva företagskunder i Sverige. Företaget planerar att bygga en ny kundportal och behöver hjälp att välja mellan Microsoft Azure och Amazon Web Services (AWS) som molnplattform.

Portalen används främst av kontaktpersoner på respektive företag för att välja veckans recept samt hantera leveranser. Belastningen på systemet är extremt ojämn: den absolut hårdaste trafiken infaller på fredagar mellan kl. 10:00 och 14:00 (med en extrem topp kl. 10:00–12:00) när alla beställningar för veckan ska registreras. Det finns även en naturlig svacka under lunchtid (kl. 12:00–13:00).

Denna rapport utvärderar Azure- och AWS-arkitekturer baserat på Smakbox krav gällande kostnad, GDPR/dataresidens, skalbarhet samt minimalt driftsansvar.

---

## 2. Kravanalys & Arkitekturella Beslut

Smakbox AB har ställt upp följande sex krav för sin nya kundportal:

| Krav | Detalj | Arkitekturell lösning |
| :--- | :--- | :--- |
| **Kundportal** | Webbapplikation med inloggning, receptval och leveranshantering. | PaaS-baserad webbapp (App Service / Elastic Beanstalk). |
| **Bildlagring** | Receptbilder (JPEG) och recepthäften (PDF) upp till 50 GB. | Objektslagring med hög tillgänglighet (Blob Storage / S3). |
| **Databas** | Hantering av kunder, ordrar och prenumerationer. | Relationell SQL-databas (Azure SQL / RDS Aurora Serverless). |
| **Skalbarhet** | Klara fredagstopparna helt automatiskt utan manuell handpåläggning. | Auto-scaling på webb-tier + Serverless databasskalning. |
| **GDPR** | All data måste lagras inom EU. | Driftsättning i svenska datacenter (Sweden Central / Stockholm). |
| **Budget** | Max 500 € per månad. | Kostnadsoptimerade tier-val (Landar på < 170 € / månad). |
| **Drift** | Minimalt driftsansvar (inget serverunderhåll, OS-patchning etc.). | 100% PaaS (Platform as a Service) och serverlösa tjänster. |

---

## 3. Azure-arkitektur: Rekommenderade Tjänster & Tier

Vi föreslår följande arkitektur i Microsoft Azure, placerad i regionen **Sweden Central (Gävle/Sandviken)** för att garantera att datan lagras på svensk mark och uppfyller alla GDPR-krav med minimal latens.

### A. Webbapp: Azure App Service (Plan: Standard S1)
* **Vald tier:** Standard S1 (1 Core, 1.75 GB RAM, 50 GB lagring).
* **Motivering:** Vi väljer Standard-tier eftersom den stödjer **Auto-scaling** (upp till 10 instanser). Basic-tieren stödjer inte automatisk skalning, vilket är ett absolut krav för Smakbox för att klara av fredagstopparna. S1 ger en stabil bas som enkelt kan skala ut på fredagar.
* **Driftsansvar:** Microsoft sköter operativsystem, IIS/Webbserver-uppdateringar och säkerhetspatchning.

### B. Fillagring: Azure Blob Storage (Plan: Standard, Hot Tier, LRS)
* **Vald tier:** Standard storage account, Hot Access Tier, Locally Redundant Storage (LRS).
* **Motivering:** Smakbox har endast 50 GB data (receptbilder och PDF-häften). Standard-prestanda är mer än tillräckligt. Vi väljer **Hot Tier** eftersom dessa bilder och PDF-filer kommer att läsas frekvent av kunderna när de väljer recept. **LRS (lokal redundans)** väljs för att hålla nere kostnaden, då geografisk redundans (GRS) är onödigt och dyrt för denna typ av statisk media som enkelt kan säkerhetskopieras.
* **Driftsansvar:** Serverlös objektslagring. Ingen infrastruktur att underhålla.

### C. Databas: Azure SQL Database (Plan: Serverless, General Purpose, vCore-baserad)
* **Vald konfiguration:** General Purpose - Serverless, Gen5 compute (Min: 0.5 vCore, Max: 4 vCores). Auto-pause aktiverat (1 timme).
* **Motivering:** Databasbelastningen är extremt ojämn. Med en Serverless-modell skalar Azure SQL automatiskt upp antalet vCores när trafiken ökar på fredagar (t.ex. upp till 4 vCores för att hantera tunga transaktioner kl. 10:00–12:00) och skalar ner till 0.5 vCore under lugna dagar. Dessutom sätts databasen i **Auto-pause** (0 vCore, endast lagringskostnad) om ingen har använt portalen på en timme (t.ex. under nätter och helger), vilket sparar mycket pengar.
* **Driftsansvar:** Fullt hanterad relationell databas (PaaS). Automatiska backuper, indexoptimeringar och patchning ingår.

### D. Skalbarhetsstrategi för fredagstoppar (Kl. 10:00 - 14:00)
1. **Webb-tier:** En regel för automatisk skalning (Autoscale Rule) sätts upp på App Service-planen. Om CPU-användningen överstiger 70% under mer än 5 minuter, läggs en extra instans till (upp till max 3 instanser). Kl. 14:30 på fredagar körs en schemalagd regel som skalar ner portalen till 1 instans igen för att spara pengar.
2. **Databas-tier:** Azure SQL Serverless skalar steglös och omedelbart baserat på CPU/minnesbehov utan omstart av databasen, vilket hanterar den plötsliga spiken kl. 10:00 perfekt.

---

## 4. AWS-motsvarigheter

För att kunna jämföras och erbjuda en alternativ offert har vi identifierat exakta motsvarigheter inom Amazon Web Services (AWS), driftsatta i regionen **eu-north-1 (Stockholm)**:

| Azure-tjänst | AWS-motsvarighet | Teknisk kommentar / Funktion |
| :--- | :--- | :--- |
| **Azure App Service (Standard S1)** | **AWS Elastic Beanstalk** (eller **AWS App Runner**) | PaaS-plattform som hanterar koddistribution, provisionering och lastbalansering. |
| **Azure Blob Storage (Standard Hot LRS)** | **Amazon S3 Standard** | Objektslagring för receptbilder och PDF-filer med LRS-motsvarighet. |
| **Azure SQL Database (Serverless vCore)** | **Amazon Aurora Serverless v2** (eller **Amazon RDS SQL Server**) | Serverlös PostgreSQL/MySQL-kompatibel databas som skalar ACU (Aurora Capacity Units) dynamiskt. |
| **Azure Autoscale** | **AWS Auto Scaling** | Tjänst för att automatiskt skala EC2-instanser i Elastic Beanstalk baserat på belastning eller schema. |

---

## 5. Kostnadskalkyl (Månadskostnad i EUR)

Följande kalkyler bygger på antagandet om **50 GB bildlagring**, **10 GB databaslagring**, samt att systemet skalar upp 3x under fredagens 4 timmar och körs på minimikraft (eller pausas) resterande tid. Alla kostnader är omräknade till **Euro (€)** för konsistens.

### A. Azure Pricing Calculator (Sweden Central)
* **App Service (S1):** ~67 €/månad (baserat på 730 timmar/månad för 1 instans).
* **Autoscaling-tillägg (Fredagar):** ~2 €/månad (2 extra instanser i 17.3 timmar per månad).
* **Blob Storage (50 GB Hot + 100k API-anrop):** ~1 €/månad.
* **Azure SQL Database Serverless (10 GB lagring + genomsnitt 1 vCore under 200 aktiva timmar/månad):** ~76 €/månad.
* **Data Transfer (Outbound 100 GB):** ~4 €/månad.
* **Totalt Azure:** **~150 € / månad**

### B. AWS Pricing Calculator (Stockholm Region)
* **Elastic Beanstalk (t3.medium instans + ALB lastbalanserare):** ~43 €/månad.
* **AWS Auto Scaling (Fredagar - extra t3.medium):** ~1 €/månad.
* **Amazon S3 (50 GB Standard + GET/PUT anrop):** ~1 €/månad.
* **Amazon Aurora Serverless v2 (10 GB lagring + genomsnitt 1 ACU under 200 aktiva timmar/månad):** ~26 €/månad.
* **Data Transfer Out (100 GB):** ~7 €/månad.
* **Totalt AWS:** **~78 € / månad**

---

## 6. Rekommendation och Analys av Trade-offs (VG-nivå)

Vi rekommenderar starkt att Smakbox AB väljer **Microsoft Azure** som molnleverantör. Nedan följer motiveringen samt en analys av de kompromisser (trade-offs) som valet innebär.

### Varför Azure?
1. **Starkast GDPR-compliance på svensk mark:** Azure har etablerat fysiska datacenter i Gävle/Sandviken (**Sweden Central**), vilket gör att vi kan garantera att kunddata och fakturainformation aldrig lämnar landet. AWS har datacenter i Stockholm, men Microsofts avtal och svenska närvaro är historiskt starkare inom offentlig sektor och svenska företag gällande svensk dataresidens.
2. **Överlägsen Serverless SQL-databas:** Azures inbyggda SQL Serverless är extremt moget och tillåter databasen att skala ner till absolut noll (paus) under inaktivitet. Detta gör det mycket enklare och billigare att hantera den specifika fredagsspiken jämfört med att sätta upp en komplex databaslösning i AWS.
3. **Komma-igång-faktor:** För ett mindre utvecklingsteam är Azure Portal mer intuitiv och sammanhållen för PaaS-tjänster (App Service) än AWS motsvarighet (Elastic Beanstalk), som ofta kräver djupare förståelse för nätverk (VPC, IAM-roller, EC2-detaljer).

---

### Trade-offs: PaaS vs. IaaS & Vendor Lock-in
Att välja PaaS-tjänster som App Service och Azure SQL innebär viktiga strategiska kompromisser:

1. **Vendor Lock-in (Leverantörsinlåsning):** Genom att bygga applikationen tätt kopplad till Azure App Service och Azure SQL Database gör vi det svårare att flytta till AWS eller Google Cloud i framtiden. SQL Server-specifika funktioner och Azure-portalsintegrationer är unika.
   * *Motivering av trade-off:* För Smakbox är det värt denna risk. De har inget eget IT-driftteam. Att spara 50 timmar arbetstid per år på att slippa patcha servrar och konfigurera databaskluster är värt risken att bli "inlåst" hos Microsoft.
2. **Kostnad vs. Kontroll:** AWS är något billigare i kalkylen (~78 € vs ~150 €). Detta beror på att AWS Aurora Serverless v2 har en lägre startkostnad för små databaser och att EC2-resurser i Stockholm har en aggressivare prissättning.
   * *Motivering av trade-off:* Skillnaden på 72 € per månad är försumbar för Smakbox eftersom båda ligger långt under budgeten på 500 €. Den extra kostnaden i Azure betalar för enkelheten i administrationen och den högre garantin av lokal dataresidens i Gävle.

---

## 7. Bidragsdokumentation (Arbetsfördelning)

* **Josef Alhusseini (Molnkonsult):**
  * Genomfört hela kravanalysen och tagit fram den rekommenderade Azure-arkitekturen.
  * Mappat Azure-tjänsterna till AWS-motsvarigheter (Elastic Beanstalk, S3, Aurora Serverless).
  * Genomfört kostnadsberäkningarna i respektive priskalkylator (Azure Pricing Calculator och AWS Calculator).
  * Sammanställt jämförelsetabellen och priskalkylerna i rapporten.
  * Formulerat skalbarhetsreglerna för fredagstopparna.
  * Skrivit avsnittet om Trade-offs (PaaS vs IaaS) samt slutsats/rekommendation.
