# Presentation: Molnstrategi för Smakbox AB
**Josef Alhusseini & Kevin Hermansson**

Här är det exakta textinnehållet och layouten för dina PowerPoint / Google Slides. Kopiera och klistra in innehållet från respektive slide nedan till dina slides.

---

### **Slide 1: Titelsida**
* **Titel:** Molnstrategi & Utvärdering
* **Undertitel:** Rekommendation av molnplattform för Smakbox AB kundportal
* **Presenteras av:** Josef Alhusseini och Kevin Hermansson

---

### **Slide 2: Utmaningen & Kraven**
* **Rubrik:** Smakbox AB — Krav & Utmaningar
* **Innehållspunkter:**
  * **Hög belastning på fredagar (10:00 - 14:00):** Kunderna lägger alla veckobeställningar samtidigt. Systemet måste skala automatiskt för att klara topparna.
  * **Minimalt underhåll (PaaS):** Smakbox har inget eget driftteam. Molnleverantören måste hantera operativsystem och uppdateringar.
  * **GDPR & Lagring:** 50 GB receptbilder (JPEG) och recepthäften (PDF). All data måste sparas inom EU (Sverige önskvärt).
  * **Budget:** Max 500 € / månad (ca 5 800 SEK).

---

### **Slide 3: Utvärderad Azure-arkitektur**
* **Rubrik:** Arkitektur i Microsoft Azure
* **Innehållspunkter:**
  * **Webbportal:** **Azure App Service (Standard S1, Linux)**
    * Stödjer automatisk skalning (1 instans normalt, upp till 3 på fredagar).
  * **Databas:** **Azure SQL Database (Serverless 0.5–2 vCores)**
    * Skalar datorkraft automatiskt vid belastning. Pausas vid inaktivitet.
  * **Bild- & PDF-lagring:** **Azure Blob Storage (Hot Tier, ZRS, 50 GB)**
    * Objektslagring med zonredundans i Sweden Central.
  * **Månadskostnad:** **170,62 € / månad**

---

### **Slide 4: AWS-motsvarigheter**
* **Rubrik:** Motsvarande Tjänster i AWS
* **Jämförelsetabell:**

| Behov | Microsoft Azure | AWS Motsvarighet | AWS Konfiguration |
| :--- | :--- | :--- | :--- |
| **Webbapp (PaaS)** | App Service (Standard S1) | **AWS Elastic Beanstalk + EC2** | `t3.small` instans + ALB |
| **Relationell Databas** | Azure SQL Serverless | **Amazon RDS for PostgreSQL** | `db.t4g.medium` (Single-AZ) |
| **Objektslagring** | Blob Storage (Hot ZRS) | **Amazon S3 Standard** | 50 GB Standard Storage |
| **Automatisk skalning** | App Service Autoscale | **EC2 Auto Scaling + ALB** | Skalar upp vid fredagstopp |

---

### **Slide 5: Kostnadsjämförelse**
* **Rubrik:** Månadskostnad: Azure vs AWS
* **Kalkyl (Kevins beräkning i Pricing Calculators):**
  * **Microsoft Azure (Sweden Central):**
    * App Service (S1) + Autoscaling: 63,60 €
    * Azure SQL Serverless (0.5–2 vCores): 105,86 €
    * Blob Storage Hot ZRS (50 GB): 1,16 €
    * **Totalt Azure:** **170,62 € / månad**
  * **Amazon Web Services (Stockholm Region):**
    * Elastic Beanstalk + EC2 t3.small + ALB: 30,53 € ($35,43)
    * Amazon RDS for PostgreSQL db.t4g.medium: 44,20 € ($51,29)
    * Amazon S3 Storage (50 GB): 1,07 € ($1,24)
    * **Totalt AWS:** **ca 75,80 € / månad ($87,96)**

---

### **Slide 6: Skärmdumpar — Azure Pricing Calculator**
* **Rubrik:** Skärmdumpar från Azure Pricing Calculator
* **Bilder & Underlag:**
  * **Azure App Service (Standard S1):** `60,93 € / månad` (`./screenshots/azure_app_service.png`)
  * **Azure SQL Serverless (0.5–2 vCores):** `105,86 € / månad` (`./screenshots/azure_sql.png`)
  * **Azure Blob Storage (Hot ZRS 50 GB):** `1,16 € / månad` (`./screenshots/azure_blob_storage.png`)

---

### **Slide 7: Skärmdumpar — AWS Pricing Calculator**
* **Rubrik:** Skärmdumpar från AWS Pricing Calculator
* **Bilder & Underlag:**
  * **AWS EC2 (t3.small):** `$17,40 / månad` (`./screenshots/aws_ec2.png`)
  * **AWS Load Balancer (ALB):** `$18,03 / månad` (`./screenshots/aws_alb.png`)
  * **AWS RDS for PostgreSQL (db.t4g.medium):** `$51,29 / månad` (`./screenshots/aws_rds.png`)
  * **Amazon S3 Standard (50 GB):** `$1,24 / månad` (`./screenshots/aws_s3.png`)

---

### **Slide 8: Slutsats & Rekommendation (VG-nivå)**
* **Rubrik:** Slutsats & Trade-offs
* **Innehållspunkter:**
  * **Huvudrekommendation:** **Microsoft Azure (170,62 € / månad)**
  * **Varför Azure?**
    * **Minimalt underhåll (100% ren PaaS):** Inget behov av eget IT-driftteam eller nätverkskonfiguration.
    * **Väl inom budget:** 170 € är bara en tredjedel av budgetramen på 500 €/mån. Tidsvinsten i utveckling överväger merkostnaden.
    * **Serverless Auto-pause & Sweden Central:** Pausas till 0 kr vid inaktivitet samt full dataresidens i Gävle/Sandviken.
  * **Alternativt Spår:** **AWS (ca 75,80 € / månad)**
    * Om Smakbox prioriterar **lägsta möjliga månadskostnad** finns AWS som ett fungerande alternativ, men kräver mer infrastrukturkonfiguration (ALB/VPC).

---

### **Slide 9: Bidragsdokumentation (Arbetsfördelning)**
* **Rubrik:** Vem har gjort vad?
* **Innehållspunkter:**
  * **Kevin Hermansson (Kalkylansvarig):**
    * Researchat prissättning och utfört kostnadsberäkningarna i Azure Pricing Calculator och AWS Pricing Calculator.
    * Tagit fram månadskostnader samt underlag/skärmdumpar för alla 7 tjänster.
  * **Josef Alhusseini (Presentations- & Rapportansvarig):**
    * Kravanalys, tjänsteval (PaaS, Objektslagring, Serverless SQL, Autoscale) och AWS-mappning.
    * Författat den skriftliga rapporten (`rapport.md`) samt byggt och strukturerat presentationen.
    * Håller i muntlig presentation och redovisning av rekommendation.
  * **Gemensamt:**
    * Utvärdering av trade-offs och beslut om rekommendation av AWS.



