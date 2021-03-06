---
title: Skapa en lokal Integration Runtime
description: Lär dig hur du skapar en integration runtime med egen värd i Azure Data Factory, vilket gör att data fabriker kan komma åt data lager i ett privat nätverk.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 06/18/2019
ms.openlocfilehash: 0d04ea7d7003f274b252e057b7afced7759bfaae
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928514"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Skapa och konfigurera en integration runtime med egen värd

Integrerings körningen (IR) är den beräknings infrastruktur som Azure Data Factory använder för att tillhandahålla funktioner för data integrering i olika nätverks miljöer. Mer information om IR finns i [Översikt över integration runtime](concepts-integration-runtime.md).

En integration runtime med egen värd kan köra kopierings aktiviteter mellan ett moln data lager och ett data lager i ett privat nätverk. Det kan också skicka Transform-aktiviteter mot beräknings resurser i ett lokalt nätverk eller ett virtuellt Azure-nätverk. Installationen av en lokal integration runtime kräver en lokal dator eller en virtuell dator i ett privat nätverk.  

Den här artikeln beskriver hur du kan skapa och konfigurera en lokal IR-anslutning.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="setting-up-a-self-hosted-integration-runtime"></a>Konfigurera en integration runtime med egen värd

Använd följande procedurer för att skapa och konfigurera en integration runtime med egen värd.

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>Skapa en IR med egen värd via Azure PowerShell

1. Du kan använda Azure PowerShell för den här uppgiften. Här är ett exempel:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Ladda ned](https://www.microsoft.com/download/details.aspx?id=39717) och installera integration runtime med egen värd på en lokal dator.

3. Hämta autentiseringsnyckel och registrera den egna värdbaserade integrerings körningen med nyckeln. Här är ett PowerShell-exempel:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

### <a name="create-a-self-hosted-ir-via-azure-data-factory-ui"></a>Skapa ett IR med egen värd via Azure Data Factory användar gränssnitt

Använd följande steg för att skapa en IR med egen värd med hjälp av Azure Data Factory användar gränssnitt.

1. På sidan för att **komma igång** i Azure Data Factory UI väljer du fliken **författare** i rutan längst till vänster.

   ![Start sidans författar knapp](media/doc-common-process/get-started-page-author-button.png)

1. Välj **anslutningar** längst ned i fönstret längst till vänster och välj **integrerings körningar** i fönstret **anslutningar** . Välj **+ ny**.

   ![Skapa Integration Runtime](media/create-self-hosted-integration-runtime/new-integration-runtime.png)

1. I fönstret **integration runtime-installation** väljer **du utför data flyttning och skicka aktiviteter till externa data bearbetningar**och väljer **Fortsätt**.

1. Ange ett namn för din IR och välj **skapa**.

1. Välj länken under **alternativ 1** för att öppna Express installationen på datorn. Eller följ stegen under **Alternativ 2** för att konfigurera manuellt. Följande instruktioner baseras på manuell installation:

   ![Installation av Integration Runtime](media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png)

    1. Kopiera och klistra in nyckeln Authentication. Välj **Hämta och installera integration runtime**.

    1. Ladda ned integration runtime med egen värd på en lokal Windows-dator. Kör installationsprogrammet.

    1. Klistra in den nyckel som du sparade tidigare på sidan **registrera integration Runtime (lokal installation)** och välj **Registrera**.
    
       ![Registrera Integration Runtime](media/create-self-hosted-integration-runtime/register-integration-runtime.png)

    1. På sidan **ny integration Runtime (lokal installation) nod** väljer du **Slutför**.

1. När den egen värdbaserade integrerings körningen har registrerats visas följande fönster:

    ![Lyckad registrering](media/create-self-hosted-integration-runtime/registered-successfully.png)

### <a name="set-up-a-self-hosted-ir-on-an-azure-vm-via-an-azure-resource-manager-template"></a>Konfigurera ett IR med egen värd på en virtuell Azure-dator via en Azure Resource Manager mall

Du kan automatisera IR-installationen med egen värd på en virtuell Azure-dator med hjälp av [mallen skapa självbetjänings-IR](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). Mallen är ett enkelt sätt att ha en fullt fungerande IR-överföring i ett virtuellt Azure-nätverk. IR har funktioner för hög tillgänglighet och skalbarhet, så länge du ställer in antalet noder på 2 eller högre.

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>Konfigurera en befintlig egen värd-IR via lokal PowerShell

Du kan använda en kommando rad för att ställa in eller hantera en befintlig egen IR-anslutning. Den här användningen kan särskilt hjälpa dig att automatisera installationen och registreringen av IR-noder med egen värd.

Dmgcmd. exe ingår i installations programmet för egen värd. Den finns vanligt vis i mappen C:\Program Files\Microsoft integration Runtime\3.0\Shared\. Det här programmet har stöd för olika parametrar och kan anropas via en kommando rad med hjälp av batch-skript för Automation.

Använd programmet på följande sätt:

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["<password>"] -Loglevel <logLevel> ]
```

Här följer information om programmets parametrar och egenskaper: 

| Egenskap                                                    | Beskrivning                                                  | Krävs |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| **RegisterNewNode** "`<AuthenticationKey>`"                     | Registrera en egen värd för integration runtime-noden med den angivna autentiseringsnyckel. | Nej       |
| **RegisterNewNode** "`<AuthenticationKey>`" "`<NodeName>`"      | Registrera en egen värd för integration runtime-noden med den angivna autentiseringsnyckel och nodnamn. | Nej       |
| **EnableRemoteAccess** "`<port>`" ["`<thumbprint>`"]            | Aktivera fjärråtkomst på den aktuella noden för att konfigurera ett kluster med hög tillgänglighet. Eller aktivera inställning av autentiseringsuppgifter direkt mot IR med egen värd utan att gå via Azure Data Factory. Du gör det senare med hjälp av cmdleten **New-AzDataFactoryV2LinkedServiceEncryptedCredential** från en fjärrdator i samma nätverk. | Nej       |
| **EnableRemoteAccessInContainer** "`<port>`" ["`<thumbprint>`"] | Aktivera fjärråtkomst till den aktuella noden när noden körs i en behållare. | Nej       |
| **DisableRemoteAccess**                                         | Inaktivera fjärråtkomst till den aktuella noden. Fjärråtkomst krävs för installation av multinoder. PowerShell **-cmdleten New-AzDataFactoryV2LinkedServiceEncryptedCredential** fungerar fortfarande även om fjärråtkomst är inaktiverat. Det här beteendet är sant så länge cmdleten körs på samma dator som den lokala IR-noden. | Nej       |
| **Nyckel** "`<AuthenticationKey>`"                                 | Skriv över eller uppdatera föregående autentiseringsnyckel. Var försiktig med den här åtgärden. Din tidigare IR-nod med egen värd kan gå offline om nyckeln är en ny integrerings körning. | Nej       |
| **GenerateBackupFile** "`<filePath>`" "`<password>`"            | Skapa en säkerhets kopierings fil för den aktuella noden. Säkerhets kopian innehåller nodens nyckel och autentiseringsuppgifter för data lagring. | Nej       |
| **ImportBackupFile** "`<filePath>`" "`<password>`"              | Återställ noden från en säkerhets kopia.                          | Nej       |
| **Starta om**                                                     | Starta om värd tjänsten för integration runtime med egen värd.   | Nej       |
| **Börja**                                                       | Starta värd tjänsten för integration runtime med egen värd.     | Nej       |
| **Stanna**                                                        | Stoppa värd tjänsten för integration runtime med egen värd.        | Nej       |
| **StartUpgradeService**                                         | Starta uppgraderings tjänsten för integration runtime med egen värd.       | Nej       |
| **StopUpgradeService**                                          | Stoppa uppgraderings tjänsten för integration runtime med egen värd.        | Nej       |
| **TurnOnAutoUpdate**                                            | Aktivera automatisk uppdatering av integrerings körningen med egen värd.        | Nej       |
| **TurnOffAutoUpdate**                                           | Inaktivera automatisk uppdatering av integrerings körningen med egen värd.       | Nej       |
| **SwitchServiceAccount** "`<domain\user>`" ["`<password>`"]           | Ange dia Host service som ska köras som ett nytt konto. Använd det tomma lösen ordet "" för system konton och virtuella konton. | Nej       |


## <a name="command-flow-and-data-flow"></a>Kommando flöde och data flöde

När du flyttar data mellan lokala platser och molnet använder aktiviteten en lokal integration runtime för att överföra data mellan en lokal data källa och molnet.

Här följer en översikt över de data flödes steg som krävs för att kopiera med en egen värd-IR:

![Översikt över data flödet på hög nivå](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. En datautvecklare skapar en integration runtime med egen värd i en Azure-datafabrik med hjälp av en PowerShell-cmdlet. För närvarande har Azure Portal inte stöd för den här funktionen.
1. Data utvecklaren skapar en länkad tjänst för ett lokalt data lager. Utvecklaren gör detta genom att ange den instansen för integration runtime med egen värd som tjänsten ska använda för att ansluta till data lager.
1. Noden för integration runtime med egen värd krypterar autentiseringsuppgifterna med hjälp av Windows Data Protection Application Programming Interface (DPAPI) och sparar autentiseringsuppgifterna lokalt. Om flera noder är inställda för hög tillgänglighet synkroniseras autentiseringsuppgifterna ytterligare mellan andra noder. Varje nod krypterar autentiseringsuppgifterna med DPAPI och lagrar dem lokalt. Synkronisering av autentiseringsuppgifter är transparent för data utvecklaren och hanteras av IR med egen värd.
1. Azure Data Factory kommunicerar med integration runtime med egen värd för att schemalägga och hantera jobb. Kommunikationen sker via en kontroll kanal som använder en delad [Azure Service Bus relä](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay) anslutning. När ett aktivitets jobb måste köras Data Factory köar begäran tillsammans med information om autentiseringsuppgifter. Det gör att autentiseringsuppgifterna inte redan lagras på den lokala integrerings körningen. Den egen värdbaserade integrerings körningen startar jobbet när den söker efter kön.
1. Den lokala integrerings körningen kopierar data mellan en lokal lagrings plats och en moln lagrings plats. Kopians riktning beror på hur kopierings aktiviteten konfigureras i data pipelinen. I det här steget kommunicerar den egen värdbaserade integrerings körningen direkt med molnbaserade lagrings tjänster som Azure Blob Storage via en säker HTTPS-kanal.

## <a name="considerations-for-using-a-self-hosted-ir"></a>Att tänka på vid användning av en egen värd IR

- Du kan använda en enda integration runtime med egen värd för flera lokala data källor. Du kan också dela den med en annan data fabrik i samma Azure Active Directory-klient (Azure AD). Mer information finns i [dela en integration runtime med egen värd](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory).
- Du kan bara installera en instans av en egen värd integrerings körning på en enskild dator. Om du har två data fabriker som behöver åtkomst till lokala data källor kan du antingen använda funktionen för [IR-delning med egen värd](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory) för att dela IR-filen med egen värd, eller installera den lokala IR-enheten på två lokala datorer, en för varje data fabrik.  
- Integration runtime med egen värd måste inte finnas på samma dator som data källan. Men med den egen värdbaserade integrerings körningen nära data källan minskas tiden för integration runtime med egen värd för att ansluta till data källan. Vi rekommenderar att du installerar den lokala integrerings körningen på en dator som är en annan än den som är värd för den lokala data källan. När den lokala integrerings körningen och data källan finns på olika datorer, konkurrerar inte den lokala integrerings körningen med data källan för resurser.
- Du kan ha flera egen värdbaserade integrerings körningar på olika datorer som ansluter till samma lokala data källa. Om du till exempel har två egen värdbaserade integrerings körningar som hanterar två data fabriker, kan samma lokala data källa registreras med båda data fabrikerna.
- Om du redan har en gateway installerad på datorn för att hantera ett Power BI scenario, installerar du en separat integration runtime för egen värd för Data Factory på en annan dator.
- Använd en integration runtime med egen värd för att stödja data integrering i ett virtuellt Azure-nätverk.
- Behandla din data källa som en lokal data källa som ligger bakom en brand vägg, även när du använder Azure-ExpressRoute. Använd integration runtime med egen värd för att ansluta tjänsten till data källan.
- Använd integration runtime med egen värd även om data lagret finns i molnet på en virtuell IaaS-dator (Azure Infrastructure as a Service).
- Aktiviteter kan Miss Miss kan utföras i en integration runtime med egen värd som du har installerat på en Windows Server för vilken FIPS-kompatibel kryptering har Aktiver ATS. Undvik det här problemet genom att inaktivera FIPS-kompatibel kryptering på servern. Om du vill inaktivera FIPS-kompatibel kryptering ändrar du följande register under nyckels värde från 1 (aktiverat) till 0 (inaktive rad): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Krav

- De Windows-versioner som stöds är:
  + Windows 7 Service Pack 1
  + Windows 8.1
  + Windows 10
  + Windows Server 2008 R2 SP1
  + Windows Server 2012
  + Windows Server 2012 R2
  + Windows Server 2016
  + Windows Server 2019
   
   Installation av integration runtime med egen värd på en domänkontrollant stöds inte.
- .NET Framework 4.6.1 eller senare krävs. Om du installerar integration runtime med egen värd på en dator med Windows 7 installerar du .NET Framework 4.6.1 eller senare. Mer information finns i [.NET Framework system krav](/dotnet/framework/get-started/system-requirements) .
- Den rekommenderade minsta konfigurationen för den egen värdbaserade integration runtime-datorn är en processor på 2 GHz med fyra kärnor, 8 GB RAM-minne och 80 GB ledigt hårddisk utrymme.
- Om värd datorn försätts i vilo läge svarar inte den egen värdbaserade integrerings körningen med data begär Anden. Konfigurera ett lämpligt energi schema på datorn innan du installerar integration runtime med egen värd. Om datorn är konfigurerad att försättas i vilo läge visas ett meddelande om att installations programmet för egen värd för integration runtime körs.
- Du måste vara administratör på datorn för att kunna installera och konfigurera integration runtime med egen värd.
- Kopierings aktivitets körningar sker med en angiven frekvens. Processor-och RAM-användning på datorn följer samma mönster med hög belastnings tider. Resursanvändningen beror också mycket på mängden data som flyttas. När flera kopierings jobb pågår, ser du att resursanvändningen går upp under hög belastnings tider.
- Aktiviteter kan Miss Miss kan uppstå under extrahering av data i Parquet-, ORC-eller Avro-format. Mer information om Parquet finns i [Parquet-format i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime). Skapandet av filen körs på den lokala integrations datorn. För att fungera som förväntat, kräver fil skapande följande krav:
    - [Visual C++ 2010 Redistributable](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) Paket (x64)
    - Java Runtime (JRE) version 8 från en JRE-Provider, till exempel [anta openjdk](https://adoptopenjdk.net/). Kontrol lera att variabeln `JAVA_HOME`s miljö har angetts.

## <a name="installation-best-practices"></a>Metodtips för installation

Du kan installera integration runtime med egen värd genom att ladda ned ett MSI-installationspaketet från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Se artikeln [Flytta data mellan lokalt och i molnet](tutorial-hybrid-copy-powershell.md) för stegvisa instruktioner.

- Konfigurera ett energi schema på värddatorn för den lokala integrerings körningen så att datorn inte försätts i vilo läge. Om värddatorn försätts i vilo läge är den egna värdbaserade integrerings körningen offline.
- Säkerhetskopiera regelbundet de autentiseringsuppgifter som är associerade med den egen värdbaserade integrerings körningen.
- Information om hur du automatiserar åtgärder för IR-installation med egen värd finns i [Konfigurera en befintlig egen värd för IR via PowerShell](#setting-up-a-self-hosted-integration-runtime).  

## <a name="install-and-register-a-self-hosted-ir-from-microsoft-download-center"></a>Installera och registrera en egen värd-IR från Microsoft Download Center

1. Gå till [hämtnings sidan för Microsoft integration runtime](https://www.microsoft.com/download/details.aspx?id=39717).
1. Välj **Hämta**, Välj 64-bitars versionen och välj **Nästa**. 32-bitars versionen stöds inte.
1. Kör MSI-filen direkt eller spara den på hård disken och kör den.
1. I fönstret **Välkommen** väljer du ett språk och väljer **Nästa**.
1. Godkänn licens villkoren för program vara från Microsoft och välj **Nästa**.
1. Välj **mapp** för att installera integration runtime med egen värd och välj **Nästa**.
1. På sidan **klar att installera** väljer du **Installera**.
1. Slutför installationen genom att klicka på **Slutför** .
1. Hämta nyckeln för autentisering med hjälp av PowerShell. Här är ett PowerShell-exempel för att hämta nyckeln för autentisering:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```

1. I fönstret **registrera integration Runtime (lokal installation)** för Microsoft integration runtime Configuration Manager som körs på datorn utför du följande steg:

    1. Klistra in nyckeln Authentication i text arean.

    1. Du kan också välja **Visa autentiseringsnyckel** för att se nyckel texten.

    1. Välj **Registrera**.

## <a name="high-availability-and-scalability"></a>Hög tillgänglighet och skalbarhet

Du kan associera en egen värd integrerings körning med flera lokala datorer eller virtuella datorer i Azure. De här datorerna kallas noder. Du kan ha upp till fyra noder som är kopplade till en egen värd integrerings körning. Fördelarna med att ha flera noder på lokala datorer som har en gateway installerad för en logisk Gateway är:

* Högre tillgänglighet för integration runtime med egen värd så att den inte längre är en enskild felpunkt i din Big data-lösning eller integrering av moln data med Data Factory. Denna tillgänglighet säkerställer kontinuitet när du använder upp till fyra noder.
* Bättre prestanda och data flöde vid data förflyttning mellan lokala och molnbaserade data lager. Få mer information om [prestanda jämförelser](copy-activity-performance.md).

Du kan associera flera noder genom att installera program varan för egen värd integrerings körning från [Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Registrera sedan den med hjälp av någon av de autentiseringsinställningar som hämtades från cmdleten **New-AzDataFactoryV2IntegrationRuntimeKey** , enligt beskrivningen i [självstudien](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> Du behöver inte skapa en ny integration runtime med egen värd för att associera varje nod. Du kan installera integration runtime med egen värd på en annan dator och registrera den med samma autentiseringsnyckel.

> [!NOTE]
> Innan du lägger till en annan nod för hög tillgänglighet och skalbarhet måste du kontrol lera att alternativet **fjärråtkomst till intranät** är aktiverat på den första noden. Det gör du genom att välja **Microsoft Integration Runtime Configuration Manager** > **Inställningar** > **fjärråtkomst till intranätet**.

### <a name="scale-considerations"></a>Skalnings överväganden

#### <a name="scale-out"></a>Skala ut

När processor användningen är hög och tillgängligt minne är lågt på den lokala IR-filen lägger du till en ny nod för att skala belastningen mellan datorer. Om aktiviteterna inte kan utföras på grund av att tids gränsen uppnåddes eller IR-noden med egen värd är offline, hjälper det om du lägger till en nod i gatewayen.

#### <a name="scale-up"></a>Skala upp

När processorn och det tillgängliga RAM-minnet inte är väl utnyttjat, men körningen av samtidiga jobb når en nods gränser, skala upp genom att öka antalet samtidiga jobb som en nod kan köra. Du kanske också vill skala upp när aktiviteternas tids gräns beror på att IR är överbelastad. Som du ser i följande bild kan du öka den maximala kapaciteten för en nod:  

![Öka antalet samtidiga jobb som kan köras på en nod](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Krav för TLS/SSL-certifikat

Här följer kraven för TLS/SSL-certifikatet som du använder för att skydda kommunikationen mellan noderna för integration Runtime:

- Certifikatet måste vara ett offentligt betrott X509 v3-certifikat. Vi rekommenderar att du använder certifikat som utfärdats av en offentlig partner certifikat utfärdare (CA).
- Varje nod för integration runtime måste ha förtroende för det här certifikatet.
- Vi rekommenderar inte certifikat för certifikat mottagarens alternativa namn eftersom endast det sista SAN-objektet används. Alla andra SAN-objekt ignoreras. Om du till exempel har ett SAN-certifikat vars San-nätverk är **node1.domain.contoso.com** och **node2.domain.contoso.com**, kan du bara använda det här certifikatet på en dator vars fullständigt kvalificerade domän namn (FQDN) är **node2.domain.contoso.com**.
- Certifikatet kan använda en nyckel storlek som stöds av Windows Server 2012 R2 för SSL-certifikat.
- Certifikat som använder CNG-nycklar stöds inte.  

> [!NOTE]
> Det här certifikatet används:
>
> - För att kryptera portar på en IR-nod med egen värd.
> - För nod-till-nod-kommunikation för tillstånds synkronisering, som innehåller synkronisering av autentiseringsuppgifter för länkade tjänster mellan noder.
> - När en PowerShell-cmdlet används för inställningar för länkad autentiseringsuppgift inifrån ett lokalt nätverk.
>
> Vi rekommenderar att du använder det här certifikatet om din privata nätverks miljö inte är säker eller om du vill skydda kommunikationen mellan noderna i ditt privata nätverk.
>
> Data förflyttning under överföring från en egen värd-IR till andra data lager sker alltid i en krypterad kanal, oavsett om det här certifikatet har angetts eller inte.

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Skapa en delad integration runtime med egen värd i Azure Data Factory

Du kan återanvända en befintlig egen värd för integrerings körning som du redan har konfigurerat i en data fabrik. Med den här återkopplingen kan du skapa en länkad egen värd för integrerings körning i en annan data fabrik genom att referera till en befintlig delad IR med egen värd.

Om du vill se en introduktion och demonstration av den här funktionen kan du titta på följande 12-minuters video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologi

- **Delad IR**: en ursprunglig, egen IR-IR som körs på en fysisk infrastruktur.  
- **Länkad IR**: en IR som refererar till en annan delad IR. Den länkade IR-filen är en logisk IR och använder infrastrukturen för en annan delad IR-anslutning med egen värd.

### <a name="methods-to-share-a-self-hosted-integration-runtime"></a>Metoder för att dela en integration runtime med egen värd

Om du vill dela en integration runtime med egen värd med flera data fabriker kan du läsa mer i [skapa en delad integration runtime med egen värd](create-shared-self-hosted-integration-runtime-powershell.md) .

### <a name="monitoring"></a>Övervakning

#### <a name="shared-ir"></a>Delad IR

![Alternativ för att hitta en delad integrerings körning](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

![Övervaka en delad integrerings körning](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

#### <a name="linked-ir"></a>Länkad IR

![Alternativ för att hitta en länkad integrerings körning](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

![Övervaka en länkad integrerings körning](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Kända begränsningar för IR-delning med egen värd

* Data fabriken där en länkad IR skapas måste ha en [MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Som standard har data fabriker som skapats i Azure Portal-eller PowerShell-cmdlets en implicit skapad MSI. Men när en data fabrik skapas via en Azure Resource Manager mall eller SDK, måste du ange egenskapen **Identity** explicit. Den här inställningen säkerställer att Resource Manager skapar en data fabrik som innehåller en MSI.

* Den Data Factory .NET SDK som stöder den här funktionen måste vara version 1.1.0 eller senare.

* Om du vill bevilja behörighet behöver du ägar rollen eller den ärvda ägar rollen i data fabriken där den delade IR-filen finns.

* Delnings funktionen fungerar endast för data fabriker inom samma Azure AD-klient.

* För Azure AD- [gäst användare](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)är Sök funktionen i användar gränssnittet, som visar alla data fabriker med hjälp av nyckelordet search, [inte fungerar](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits). Men så länge gäst användaren är ägare till data fabriken kan du dela IR-filen utan Sök funktionen. För MSI-filen för data fabriken som behöver dela IR-filen anger du detta MSI i rutan **tilldela behörighet** och väljer **lägg till** i Data Factory användar gränssnitt.

  > [!NOTE]
  > Den här funktionen är endast tillgänglig i Data Factory v2.

## <a name="notification-area-icons-and-notifications"></a>Ikoner och meddelanden i meddelande fältet

Om du flyttar markören över ikonen eller meddelandet i meddelande fältet kan du se information om status för den lokala integrerings körningen.

![Meddelanden i meddelande fältet](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewalls"></a>Portar och brand väggar

Det finns två brand väggar att tänka på:

- *Företags brand väggen* som körs på den centrala routern i organisationen
- *Windows-brandväggen* som har kon figurer ATS som daemon på den lokala datorn där integration runtime med egen värd är installerad

![Brand väggarna](media/create-self-hosted-integration-runtime/firewall.png)

På företags brand Väggs nivå måste du konfigurera följande domäner och utgående portar:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

På Windows brand Väggs nivå eller på dator nivå är dessa utgående portar normalt aktiverade. Om de inte är det kan du konfigurera domäner och portar på en egen värd för integration runtime-datorn.

> [!NOTE]
> Utifrån din källa och dina mottagare kan du behöva tillåta ytterligare domäner och utgående portar i företags brand väggen eller Windows-brandväggen.
>
> För vissa moln databaser, till exempel Azure SQL Database och Azure Data Lake, kan du behöva tillåta IP-adresser för datorer med egen värd integrerings körning i brand Väggs konfigurationen.

### <a name="copy-data-from-a-source-to-a-sink"></a>Kopiera data från en källa till en mottagare

Se till att du aktiverar brand Väggs regler korrekt i företags brand väggen, Windows-brandväggen för den lokala integration runtime-datorn och själva data lagret. Genom att aktivera de här reglerna kan integrerings körningen med egen värd ansluta till både källa och mottagare. Aktivera regler för varje data lager som ingår i kopierings åtgärden.

Gör så här om du till exempel vill kopiera från ett lokalt data lager till en SQL Database mottagare eller en Azure SQL Data Warehouse mottagare:

1. Tillåt utgående TCP-kommunikation på port 1433 för både Windows-brandväggen och företags brand väggen.
1. Konfigurera brand Väggs inställningarna för SQL-databasen för att lägga till IP-adressen för den egen värdbaserade integration runtime-datorn i listan över tillåtna IP-adresser.

> [!NOTE]
> Om brand väggen inte tillåter utgående port 1433, kan inte den egen värdbaserade integrerings körningen komma åt SQL Database direkt. I det här fallet kan du använda en [mellanlagrad kopia](copy-activity-performance.md) för att SQL Database och SQL Data Warehouse. I det här scenariot behöver du bara HTTPS (port 443) för data flytten.

## <a name="proxy-server-considerations"></a>Överväganden för proxyserver

Om företagets nätverks miljö använder en proxyserver för att få åtkomst till Internet konfigurerar du den egen värdbaserade integrerings körningen så att rätt proxyinställningar används. Du kan ställa in proxyn under den inledande registrerings fasen.

![Ange proxy](media/create-self-hosted-integration-runtime/specify-proxy.png)

När det konfigureras använder den lokal integrerings körningen proxyservern för att ansluta till moln tjänstens källa och mål (som använder HTTP-eller HTTPS-protokollet). Detta är anledningen till att du väljer **ändra länk** under den första installationen.

![Ange proxy](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Det finns tre konfigurations alternativ:

- **Använd inte proxy**: den egna värdbaserade integrerings körningen använder inte uttryckligen någon proxy för att ansluta till moln tjänster.
- **Använd systemproxy**: den egna värdbaserade integrerings körningen använder den proxyserver som kon figurer ATS i diahost. exe. config och diawp. exe. config. Om dessa filer inte anger någon proxykonfiguration ansluter den egna värdbaserade integrerings körningen till moln tjänsten direkt utan att gå via en proxy.
- **Använd anpassad proxy**: konfigurera inställningen för http-proxy som ska användas för integration runtime med egen värd i stället för konfigurationer i diahost. exe. config och diawp. exe. config. **Adress-** och **port** värden måste anges. Värdena för **användar namn** och **lösen ord** är valfria, beroende på proxyns autentiseringsinställningar. Alla inställningar krypteras med Windows DPAPI på integration runtime med egen värd och lagras lokalt på datorn.

Värd tjänsten för integration runtime startas om automatiskt när du har sparat de uppdaterade proxyinställningarna.

När du har registrerat en integration runtime med egen värd, om du vill visa eller uppdatera proxyinställningar, använder du Microsoft Integration Runtime Configuration Manager.

1. Öppna **Microsoft Integration Runtime Configuration Manager**.
1. Välj fliken **Inställningar**.
1. Under **http-proxy**väljer du länken **ändra** för att öppna dialog rutan **ange http-proxy** .
1. Välj **Nästa**. Sedan visas en varning som ber om din tillåtelse att spara proxyinställningarna och starta om värd tjänsten för integration Runtime.

Du kan använda Configuration Manager-verktyget för att visa och uppdatera HTTP-proxyn.

![Visa och uppdatera proxyn](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Om du konfigurerar en proxyserver med NTLM-autentisering körs värd tjänsten för integration runtime under domän kontot. Om du senare ändrar lösen ordet för domän kontot måste du komma ihåg att uppdatera konfigurations inställningarna för tjänsten och starta om tjänsten. På grund av detta krav rekommenderar vi att du använder proxyservern genom att använda ett dedikerat domän konto som inte kräver att du uppdaterar lösen ordet ofta.

### <a name="configure-proxy-server-settings"></a>Konfigurera inställningar för proxyserver

Om du väljer alternativet **Använd systemproxy** för HTTP-proxyn använder den egen värdbaserade integrerings körningen proxyinställningarna i diahost. exe. config och diawp. exe. config. När de här filerna inte anger någon proxy ansluter den egna värdbaserade integrerings körningen till moln tjänsten direkt utan att gå via en proxy. Följande procedur innehåller instruktioner för att uppdatera filen diahost. exe. config:

1. I Utforskaren skapar du en säker kopia av C:\Program\Microsoft integration Runtime\3.0\Shared\diahost.exe.config som en säkerhets kopia av den ursprungliga filen.
1. Öppna anteckningar som körs som administratör.
1. I anteckningar öppnar du text filen C:\Program Files\Microsoft integration Runtime\3.0\Shared\diahost.exe.config.
1. Hitta standard tag gen **system.net** -taggen som visas i följande kod:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Du kan sedan lägga till information om proxyservern som visas i följande exempel:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Proxy-taggen tillåter ytterligare egenskaper för att ange nödvändiga inställningar som `scriptLocation`. Se [\<proxy\> element (nätverks inställningar)](https://msdn.microsoft.com/library/sa91de1e.aspx) för syntax.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
1. Spara konfigurations filen på den ursprungliga platsen. Starta sedan om värd tjänsten för integration runtime med egen värd, som hämtar ändringarna.

   Om du vill starta om tjänsten använder du tjänst-appleten från kontroll panelen. Eller från Integration Runtime Configuration Manager, Välj knappen **stoppa tjänst** och välj sedan **Starta tjänst**.

   Om tjänsten inte startar har du troligt vis lagt till felaktig syntax för XML-taggar i program konfigurations filen som du redigerade.

> [!IMPORTANT]
> Glöm inte att uppdatera både diahost. exe. config och diawp. exe. config.

Du måste också se till att Microsoft Azure finns i företagets lista över tillåtna. Du kan ladda ned listan med giltiga Azure IP-adresser från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>Möjliga symptom på problem som rör brand väggen och proxyservern

Om du ser fel meddelanden som följande, är den troligaste orsaken felaktig konfiguration av brand väggen eller proxyservern. Sådan konfiguration förhindrar att integrerings körningen med egen värd ansluter till Data Factory att autentisera sig själv. Se föregående avsnitt för att kontrol lera att din brand vägg och proxyserver är korrekt konfigurerade.

* När du försöker registrera den egna värdbaserade integrerings körningen visas följande fel meddelande: "Det gick inte att registrera den här Integration Runtime noden! Bekräfta att autentiseringsnyckel är giltig och att värd tjänsten för integrations tjänsten körs på den här datorn. "
* När du öppnar Integration Runtime Configuration Manager visas statusen **frånkopplad** eller **ansluten**. När du visar händelse loggar för Windows, under **Loggboken** > **program-och tjänst loggar** > **Microsoft integration runtime**, visas fel meddelanden som detta:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-an-intranet"></a>Aktivera fjärråtkomst från ett intranät

Om du använder PowerShell för att kryptera autentiseringsuppgifter från en nätverksansluten dator än den där du har installerat integration runtime med egen värd, kan du aktivera alternativet **fjärråtkomst från intranät** . Om du kör PowerShell för att kryptera autentiseringsuppgifter på den dator där du har installerat integration runtime med egen värd kan du inte aktivera **fjärråtkomst från intranätet**.

Aktivera **fjärråtkomst från intranätet** innan du lägger till en annan nod för hög tillgänglighet och skalbarhet.  

När du kör installations programmet för den lokala integrerings körnings versionen 3,3 eller senare, inaktiverar installations programmet för den lokala integrerings körnings miljön **fjärråtkomst från intranätet** på den lokala datorn för integration Runtime.

När du använder en brand vägg från en partner eller andra kan du manuellt öppna port 8060 eller porten som är konfigurerad för användaren. Om du har problem med brand väggen när du konfigurerar den egna värdbaserade integrerings körningen använder du följande kommando för att installera integration runtime med egen värd utan att konfigurera brand väggen:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Om du väljer att inte öppna port 8060 på den lokala integration runtime-datorn använder du andra metoder än inställningen ange autentiseringsuppgifter för att konfigurera autentiseringsuppgifter för data lagring. Du kan till exempel använda PowerShell-cmdleten **New-AzDataFactoryV2LinkedServiceEncryptCredential** .

## <a name="next-steps"></a>Nästa steg

Steg för steg-anvisningar finns i [Självstudier: Kopiera lokala data till molnet](tutorial-hybrid-copy-powershell.md).
