# Presentation: Molnstrategi för Smakbox AB
**Josef Alhusseini**

Här är det exakta textinnehållet och layouten för dina PowerPoint / Google Slides. Kopiera och klistra in innehållet från respektive slide nedan till dina slides.

---

### **Slide 1: Titelsida**
* **Titel:** Molnstrategi & Utvärdering
* **Undertitel:** Rekommendation av molnplattform för Smakbox AB kundportal
* **Presenteras av:** Josef Alhusseini

---

### **Slide 2: Utmaningen & Kraven**
* **Rubrik:** Smakbox AB — Krav & Utmaningar
* **Innehållspunkter:**
  * **Hög belastning på fredagar (10:00 - 14:00):** Kunderna lägger alla veckobeställningar samtidigt. Systemet måste skala automatiskt för att klara topparna.
  * **Minimalt underhåll (PaaS):** Smakbox har inget eget driftteam. Molnleverantören måste hantera operativsystem och uppdateringar.
  * **GDPR & Lagring:** 50 GB receptbilder (JPEG) och recepthäften (PDF). All data måste sparas inom EU (Sverige önskvärt).
  * **Budget:** Max 500 € / månad (ca 5 800 SEK).

---

### **Slide 3: Föreslagen Azure-arkitektur**
* **Rubrik:** Rekommenderad Arkitektur i Azure
* **Innehållspunkter:**
  * **Webbportal:** **Azure App Service (Standard S1)**
    * Stödjer automatisk skalning upp till 10 instanser under fredagar.
  * **Databas:** **Azure SQL Database (Serverless vCore)**
    * Skalar datorkraft steglöst (0.5–4 vCores) under belastning. Pausas automatiskt under nätter/helger för att spara pengar.
  * **Bild- & PDF-lagring:** **Azure Blob Storage (Standard Hot, LRS)**
    * Högpresterande och mycket billig objektslagring för 50 GB statiska filer.
  * **Region:** **Sweden Central (Gävle/Sandviken)**
    * All data på svensk mark — 100% GDPR-compliant.

---

### **Slide 4: AWS-motsvarigheter**
* **Rubrik:** Motsvarande Tjänster i AWS
* **Jämförelsetabell:**

| Behov | Microsoft Azure | AWS Motsvarighet |
| :--- | :--- | :--- |
| **Webbapp (PaaS)** | App Service (Standard S1) | **AWS Elastic Beanstalk** (eller **App Runner**) |
| **Relationell Databas** | Azure SQL Serverless | **Amazon Aurora Serverless v2** |
| **Objektslagring** | Blob Storage (Hot LRS) | **Amazon S3 Standard** |
| **Automatisk skalning** | Azure Autoscale | **AWS Auto Scaling** |

---

### **Slide 5: Kostnadsjämförelse**
* **Rubrik:** Månadskostnad: Azure vs AWS
* **Kalkyl:**
  * **Microsoft Azure (Sweden Central):**
    * App Service (S1) + Autoscaling: ~75 USD
    * Azure SQL Serverless (1 vCore avg, 10 GB): ~83 USD
    * Blob Storage (50 GB): ~2 USD
    * **Totalt Azure:** **~160 USD / månad (ca 150 €)**
  * **Amazon Web Services (Stockholm Region):**
    * Elastic Beanstalk (t3.medium + ALB): ~47 USD
    * Aurora Serverless (1 ACU avg, 10 GB): ~28 USD
    * S3 Storage (50 GB): ~3 USD
    * **Totalt AWS:** **~80 USD / månad (ca 75 €)**

---

### **Slide 6: Slutsats & Rekommendation (VG-nivå)**
* **Rubrik:** Slutsats & Trade-offs
* **Innehållspunkter:**
  * **Rekommendation:** **Microsoft Azure**
  * **Varför?**
    * **GDPR & Dataresidens:** Datacenter i Gävle ger starkare garantier för lokal lagring i Sverige än AWS.
    * **Användarvänlighet:** Azure SQL Serverless har bättre inbyggd auto-pausfunktion för att spara pengar, och Azure-portalen är mer lätthanterlig för mindre team.
  * **Strategisk Trade-off (Vendor Lock-in):**
    * Genom att välja PaaS (App Service, Azure SQL) slipper vi driftsansvar (OS-patchar och databaskluster), vilket sparar tid.
    * Men vi får **Vendor Lock-in** hos Microsoft, vilket gör det svårare att migrera till AWS eller Google Cloud i framtiden. För Smakbox är enkelheten värd denna inlåsning.
