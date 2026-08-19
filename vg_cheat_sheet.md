# VG-Underlag: Följdfrågor & Svar för Redovisningen
**Smakbox AB Molnstrategi**

För att säkra ett **VG** krävs det att du kan resonera kring dina val och svara på lärarens följdfrågor under redovisningen. Här är de vanligaste frågorna läraren kan ställa, och exakt hur du ska svara på dem på en professionell nivå.

---

### Fråga 1: "Du valde PaaS (App Service/Elastic Beanstalk). Varför inte IaaS (virtuella maskiner/EC2) som ofta är billigare per timme?"
* **Svar:** "Det stämmer att rå beräkningskraft på en virtuell maskin kan ha en lägre timkostnad. Men för Smakbox handlar det om *Total Cost of Ownership (TCO)*. De har inget eget driftteam. Om vi väljer IaaS måste någon manuellt konfigurera brandväggar, installera operativsystemspatchar, uppdatera webbservern och sätta upp lastbalanserare. Arbetstiden det tar äter snabbt upp de få kronor vi sparar. Genom att betala lite mer för PaaS köper vi oss fria från driftsansvar så utvecklarna kan fokusera helt på applikationslogiken och kundportalen."

---

### Fråga 2: "Vad innebär 'Vendor Lock-in' i din rekommendation, och hur kan Smakbox hantera det?"
* **Svar:** "Eftersom vi använder Azures färdiga PaaS-tjänster som App Service och Azure SQL Database, bygger vi kod och konfigurationer som är specifika för Microsofts miljö. Om vi i framtiden vill byta till AWS Aurora eller Google Cloud SQL måste vi skriva om delar av databaskopplingen och ändra driftsättningsskripten. Vi hanterar det genom att acceptera kompromissen: risken för inlåsning är värd enkelheten just nu. Om vi velat undvika det helt hade vi behövt köra Kubernetes (AKS) och en oberoende databas, vilket hade varit alldeles för komplext och dyrt för Smakbox storlek."

---

### Fråga 3: "Du valde Locally Redundant Storage (LRS) för bildlagringen. Vad händer om hela datacentret i Gävle brinner ner? Förlorar vi receptbilderna då?"
* **Svar:** "LRS replikerar datan tre gånger inom samma fysiska datacenter. Det skyddar mot hårddiskhaverier och lokala strömavbrott, men inte mot en total katastrof som förstör hela centret. För Smakbox är detta en medveten riskoptimering. Eftersom bilderna och PDF-filerna är statisk media som utvecklarna har kopior på lokalt och i sitt Git-arkiv, kan vi enkelt ladda upp dem igen om en katastrof skulle ske. Det är inte värt att betala dubbelt så mycket för geografisk redundans (GRS) för data som inte är transaktionell eller unik."

---

### Fråga 4: "Varför valde du en Serverless databas istället for en etablerad fast storlek (Provisioned/Single Database)?"
* **Svar:** "Smakbox har en extremt ojämn belastningsprofil. Trafiken är nästan noll under nätter och vardagar, men spikar enormt under 4 timmar på fredagar. Om vi hade valt en traditionell fast databas hade vi varit tvungna att betala för en stor och dyr databas dygnet runt bara för att klara fredagsspiken. Med Serverless betalar vi endast för de sekunder som databasen faktiskt jobbar. Den skalar automatiskt upp till 4 vCores på fredagen och pausar helt när ingen använder systemet på helger och kvällar."

---

### Fråga 5: "AWS är ju nästan hälften så dyrt i din kalkyl (~75 € mot Azures ~150 €). Varför rekommenderar du ändå Azure?"
* **Svar:** "Kostnaden är inte det enda argumentet. Skillnaden på 75 € i månaden är försumbar för Smakbox då båda ligger långt under budgeten på 500 €. Med Azure får vi fysisk dataresidens i Sverige (Sweden Central), vilket är en stor fördel gällande GDPR-compliance och minskar latensen för de svenska användarna. Dessutom är Azure SQL Serverless mer moget och har en mer klockren auto-pausfunktion som gör administrationen enklare för ett mindre team jämfört med att sätta upp AWS Aurora Serverless."
