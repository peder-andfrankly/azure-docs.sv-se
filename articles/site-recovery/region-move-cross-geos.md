---
title: Flytta virtuella Azure-datorer mellan myndigheter och offentliga regioner med Azure Site Recovery
description: Använd Azure Site Recovery för att flytta virtuella Azure-datorer mellan Azure myndigheter och offentliga regioner.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 2a749e9345fec0e91751641cd15805d7f7d62d95
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961417"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Flytta virtuella Azure-datorer mellan Azure Government och offentliga regioner 

Du kanske vill flytta dina virtuella IaaS-datorer mellan Azure Government och offentliga regioner för att öka tillgängligheten för dina befintliga virtuella datorer, förbättra hanterbarheten eller av styrnings orsaker som beskrivs [här](azure-to-azure-move-overview.md).

Utöver att använda tjänsten [Azure Site Recovery](site-recovery-overview.md) för att hantera och samordna haveriberedskap på lokala datorer och virtuella Azure-datorer i syftet affärskontinuitet och haveriberedskap (BCDR) kan du även använda Site Recovery för att hantera flytt av virtuella Azure-datorer till en sekundär region.       

Den här självstudien visar hur du flyttar virtuella Azure-datorer mellan Azure Government och offentliga regioner med Azure Site Recovery. Samma kan utökas för att flytta virtuella datorer mellan region par som inte är inom samma geografiska kluster. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Kontrollera förutsättningar
> * Förbereda källregionens virtuella datorer
> * Förbereda målregionen
> * Kopiera data till målregionen
> * Testa konfigurationen
> * Utföra flytten
> * Ta bort resurserna från källregionen

> [!IMPORTANT]
> Den här självstudien visar hur du flyttar virtuella Azure-datorer mellan Azure Government och offentliga regioner, eller mellan regioner som inte stöds av den vanliga katastrof återställnings lösningen för virtuella Azure-datorer. Om det finns [stöd](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)för käll-och mål regioner kan du se det här [dokumentet](azure-to-azure-tutorial-migrate.md) för flytten. Om ditt krav är att förbättra tillgängligheten genom att flytta virtuella datorer i en tillgänglighets uppsättning till zon fästa virtuella datorer i en annan region, se själv studie kursen [här](move-azure-VMs-AVset-Azone.md).

> [!IMPORTANT]
> Du bör inte använda den här metoden för att konfigurera DR mellan ett region par som inte stöds eftersom paret är definierade och håller svar på data i åtanke, vilket är viktigt för ett DR-scenario.

## <a name="verify-prerequisites"></a>Kontrollera förutsättningar

> [!NOTE]
> Se till att du förstår [arkitekturen och komponenterna](physical-azure-architecture.md) i det här scenariot. Den här arkitekturen kommer att användas för att flytta virtuella Azure-datorer **genom att behandla de virtuella datorerna som fysiska servrar**.

- Granska [kraven för stöd](vmware-physical-secondary-support-matrix.md) för alla komponenter.
- Kontrol lera att de servrar som du vill replikera följer [kraven för virtuella Azure-datorer](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Förbereda ett konto för automatisk installation av mobilitets tjänsten på varje server som du vill replikera.

- Observera att när du har växlat till mål regionen i Azure kan du inte utföra en återställning tillbaka till käll regionen direkt. Du måste konfigurera replikeringen igen till målet.

### <a name="verify-azure-account-permissions"></a>Verifiera behörigheter för Azure-kontot

Se till att ditt Azure-konto har behörighet att replikera virtuella datorer till Azure.

- Granska de [behörigheter](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) som du behöver för att replikera datorer till Azure.
- Verifiera och ändra [rollbaserade åtkomst](../role-based-access-control/role-assignments-portal.md) behörigheter. 

### <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk

Konfigurera ett [Azure-nätverk](../virtual-network/quick-create-portal.md).

- Virtuella Azure-datorer placeras i det här nätverket när de skapas efter en redundansväxling.
- Nätverket ska finnas i samma region som Recovery Services-valvet


### <a name="set-up-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto

Konfigurera ett [Azure Storage-konto](../storage/common/storage-quickstart-create-account.md).

- Site Recovery replikerar lokala datorer till Azure Storage. Virtuella Azure-datorer skapas från lagrings platsen när redundansväxlingen sker.
- Lagringskontot måste finnas i samma region som Recovery Services-valvet.


## <a name="prepare-the-source-vms"></a>Förbereda källregionens virtuella datorer

### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitets tjänsten måste installeras på varje server som du vill replikera. Site Recovery installerar den här tjänsten automatiskt när du aktiverar replikering för servern. Om du vill installera automatiskt måste du förbereda ett konto som Site Recovery ska använda för att få åtkomst till servern.

- Du kan använda en domän eller ett lokalt konto
- För virtuella Windows-datorer, om du inte använder ett domän konto, inaktiverar du åtkomst kontroll för fjärran vändare på den lokala datorn. Det gör du genom att lägga till DWORD-posten **LocalAccountTokenFilterPolicy**i registret under **HKEY_LOCAL_MACHINE \software\microsoft\windows\currentversion\policies\system**med värdet 1.
- Om du vill lägga till register posten för att inaktivera inställningen från en CLI skriver du: ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- För Linux bör kontot vara rot på käll-Linux-servern.


## <a name="prepare-the-target-region"></a>Förbereda målregionen

1. Verifiera att Azure-prenumerationen låter dig skapa virtuella datorer i målregionen för haveriberedskap. Kontakta supporten och aktivera den kvot som krävs.

2. Se till att din prenumeration har tillräckligt med resurser för att hantera virtuella datorer med de storlekar som de virtuella källdatorerna har. Om du använder Site Recovery till att kopiera data till målet väljer det samma storlek eller närmast möjliga storlek för den virtuella måldatorn.

3. Se till att du skapar en målresurs för varje komponent som identifieras i källans nätverkslayout. Detta är viktigt för att se till att publicera över till mål regionen, de virtuella datorerna har alla funktioner och funktioner som du hade i källan.

    > [!NOTE]
    > Azure Site Recovery identifierar och skapar automatiskt ett virtuellt nätverk när du aktiverar replikering för den virtuella källdatorn. Du kan också skapa ett nätverk i förväg och tilldela det till den virtuella datorn i arbetsflödet där du aktiverar replikering. För vissa resurser måste du dock skapa dem manuellt i målregionen, vilket beskrivs nedan.

     I följande dokument visas hur du skapar de vanligaste nätverksresurserna, baserat på den virtuella källdatorkonfigurationen.

    - [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Lastbalanserare](https://docs.microsoft.com/azure/load-balancer)
    - [Offentlig IP-adress](../virtual-network/virtual-network-public-ip-address.md)
    
    Alla andra nätverkskomponenter beskrivs i nätverkets [dokumentation](https://docs.microsoft.com/azure/#pivot=products&panel=network). 

4. Du kan [skapa ett icke-produktionsnätverk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) manuellt i målregionen om du vill testa konfigurationen innan du slutgiltigt växlar över till målregionen. Detta ger minimala störningar i produktionen och rekommenderas därför.

## <a name="copy-data-to-the-target-region"></a>Kopiera data till målregionen
I stegen nedan får du hjälp att kopiera data till målregionen med hjälp av Azure Site Recovery.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Skapa valvet i valfri region, utom i källregionen.

1. Logga in på [Azure-portalen](https://portal.azure.com) > **Recovery Services**.
2. Klicka på **Skapa en resurs** > **Hanteringsverktyg** > **Backup och Site Recovery**.
3. I **Namn** anger du det egna namnet **ContosoVMVault**. Om du har fler än en prenumeration väljer du den rätta.
4. Skapa en resursgrupp med namnet **ContosoRG**.
5. Ange en Azure-region. Information om vilka regioner som stöds finns under Geografisk tillgänglighet i avsnittet med [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
6. I Recovery Services valv klickar du på **översikt** > **ConsotoVMVault** >  **+ Replikera**
7. Välj **Till Azure** > **Inte virtualiserad/övrigt**.

### <a name="set-up-the-configuration-server-to-discover-vms"></a>Konfigurera konfigurations servern för att identifiera virtuella datorer.


Konfigurera konfigurations servern, registrera den i valvet och identifiera virtuella datorer.

1. Klicka på **Site Recovery** > **förbereda infrastruktur** > **källa**.
2. Om du inte har en konfigurations Server klickar du på **+ konfigurations Server**.
3. I **Lägg till Server**kontrollerar du att **konfigurations servern** visas i **Server typ**.
4. Hämta installations filen Site Recovery Unified setup.
5. Ladda ned valvregistreringsnyckeln. Du behöver det när du kör enhetlig installation. Nyckeln är giltig i fem dagar efter att du har genererat den.

   ![Konfigurera källan](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registrera konfigurations servern i valvet

Gör följande innan du börjar: 

#### <a name="verify-time-accuracy"></a>Kontrol lera tids exakthet
Kontrol lera att system klockan är synkroniserad med en [tids Server](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)på datorns konfigurations Server. Den ska matcha. Om det är 15 minuter framför eller bakom, kan det hända att installationen Miss känner.

#### <a name="verify-connectivity"></a>Verifiera anslutningen
Kontrol lera att datorn har åtkomst till dessa URL: er baserat på din miljö: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

IP-adressbaserade brand Väggs regler ska tillåta kommunikation till alla Azure-URL: er som anges ovan över HTTPS-port (443). För att förenkla och begränsa IP-intervallen rekommenderar vi att URL-filtrering görs.

- **Kommersiella** IP-adresser – tillåta [IP-intervall för Azure-datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653)och HTTPS-porten (443). Tillåt IP-adressintervall för Azure-regionen för din prenumeration för att stödja URL: erna AAD, backup, Replication och Storage.  
- **Myndigheter för myndigheter** – Tillåt URL: er för [Azure Government-DATAcenter](https://www.microsoft.com/en-us/download/details.aspx?id=57063)och HTTPS-porten (443) för alla USGov-regioner (Virginia, Texas, Arizona och Iowa) för att stödja URL: er för AAD, säkerhets kopiering, replikering och lagring.  

#### <a name="run-setup"></a>Kör installationsprogrammet
Kör enhetlig installation som lokal administratör för att installera konfigurations servern. Processervern och huvud mål servern installeras också som standard på konfigurations servern.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

När registreringen är klar visas konfigurations servern på sidan **inställningar** > **servrar** i valvet.

### <a name="configure-target-settings-for-replication"></a>Konfigurera mål inställningar för replikering

Välj och kontrollera målresurserna.

1. Klicka på **Förbered infrastruktur** > **Mål** och välj den Azure-prenumeration som du vill använda.
2. Ange mål distributions modell.
3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.

   ![Mål](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

1. Om du vill skapa en ny replikeringsprincip klickar du på **Site Recovery-infrastruktur** > **Replikeringsprinciper** >  **+Replikeringsprincip**.
2. I **Skapa replikeringsprincip** anger du ett principnamn.
3. I **Tröskelvärde för replikeringspunktmål** anger du gränsen för replikeringspunktmålet (RPO). Det här värdet anger hur ofta data återställnings punkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
4. I **Återställningspunkt för kvarhållning** anger du kvarhållningsperioden (i antal timmar) för varje återställningspunkt. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster. Upp till 24 timmars kvarhållning stöds för datorer replikerade till premiumlagring och 72 timmar för standardlagring.
5. I **frekvens för programkonsekventa ögonblicks bilder**anger du hur ofta återställnings punkter (i minuter) som innehåller programkonsekventa ögonblicks bilder ska skapas. Klicka på **OK** för att skapa principen.

    ![Replikeringsprincip](./media/physical-azure-disaster-recovery/replication-policy.png)


Principen associeras automatiskt med konfigurationsservern. Som standard skapas automatiskt en matchande princip för återställning efter fel. Om replikeringsprincipen till exempel är **rep-princip** skapas **en princip för återställning efter fel** . Den här principen används inte förrän du initierar en återställning efter fel från Azure.

### <a name="enable-replication"></a>Aktivera replikering

- Site Recovery installerar mobilitets tjänsten när replikering är aktive rad.
- När du aktiverar replikering för en server kan det ta 15 minuter eller längre innan ändringarna börjar gälla och visas i portalen.

1. Klicka på **Replikera program** > **Källa**.
2. I **Källa** väljer du konfigurationsservern.
3. I **typ av dator**väljer du **fysiska datorer**.
4. Välj processervern (konfigurations servern). Klicka på **OK**.
5. I **mål**väljer du den prenumeration och resurs grupp där du vill skapa virtuella Azure-datorer efter redundansväxlingen. Välj den distributions modell som du vill använda i Azure (klassisk eller resurs hantering).
6. Välj Azure-lagringskonto som du vill använda när du replikerar data. 
7. Välj det Azure-nätverk och undernät som virtuella Azure-datorer ska ansluta till efter en redundansväxling.
8. Välj **Konfigurera nu för valda datorer** om du vill använda nätverksinställningen på alla datorer som du väljer att skydda. Välj **Konfigurera senare** om du vill välja Azure-nätverket för varje dator. 
9. I **fysiska datorer**och klicka på **+ fysisk dator**. Ange namn och IP-adress. Välj operativ system för den dator som du vill replikera. Det tar några minuter för servrarna att identifieras och visas. 

   > [!WARNING]
   > Du måste ange IP-adressen för den virtuella Azure-dator som du vill flytta

10. I **egenskaper** > **Konfigurera egenskaper**väljer du det konto som ska användas av processervern för att automatiskt installera mobilitets tjänsten på datorn.
11. I **Replikeringsinställningar** > **Konfigurera replikeringsinställningar** kontrollerar du att rätt replikeringsprincip har valts. 
12. Klicka på **Aktivera replikering**. Du kan följa förloppet för jobbet **Aktivera skydd** i **Inställningar** > **Jobb** > **Site Recovery-jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.


Om du vill övervaka servrar som du lägger till kan du kontrol lera den senaste upptäckta tiden för dem i **konfigurations servrar** > **senaste kontakten på**. Om du vill lägga till datorer utan att vänta på en schemalagd identifierings tid markerar du konfigurations servern (Klicka inte på den) och klickar på **Uppdatera**.

## <a name="test-the-configuration"></a>Testa konfigurationen


1. Navigera till valvet. I **Inställningar** > **Replikerade objekt** klickar du på den virtuella dator som du vill flytta till målregionen och klickar sedan på ikonen **+ Testa redundans**.
2. I **Testa redundans** väljer du en återställningspunkt som ska användas för redundansen:

   - **Senaste bearbetade**: Redundansväxlar den virtuella datorn till den senaste återställningspunkten som bearbetades av Site Recovery-tjänsten. Tidsstämpeln visas. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstid (RTO)
   - **Senaste appkonsekventa**: Det här alternativet redundansväxlar alla virtuella datorer till den senaste appkonsekventa återställningspunkten. Tidsstämpeln visas.
   - **Anpassad**: Välj en annan återställningspunkt.

3. Välj målets virtuella Azure-nätverk, dit du vill flytta de virtuella Azure-datorerna för att testa konfigurationen. 

   > [!IMPORTANT]
   > Vi rekommenderar att du använder ett separat Azure VM-nätverk för redundanstest och inte det produktions nätverk som du vill flytta dina virtuella datorer till till och med när du aktiverade replikeringen.

4. När du vill börja testa flytten klickar du på **OK**. Om du vill spåra förloppet klickar du på den virtuella datorn för att öppna dess egenskaper. Du kan också klicka på jobbet **Testa redundans** i valvnamnet > **Inställningar** > **Jobb** > **Site Recovery-jobb**.
5. När redundansen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Kontrollera att den virtuella datorn körs med rätt storlek och ansluten till lämpligt nätverk.
6. Om du vill ta bort den virtuella dator som skapades i samband med flyttestet klickar du på **Rensa redundanstestning** i det replikerade objektet. I **Kommentarer** skriver du ned och sparar eventuella observationer från testet.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Utför flytten till målregionen och bekräfta.

1. Gå till valvet. I **Inställningar** > **Replikerade objekt** klickar du på den virtuella datorn och sedan på **Redundans**.
2. I **Redundans** väljer du **Senaste**. 
3. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker stänga av den virtuella källdatorn innan redundansen utlöses. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa förloppet för redundans på sidan **Jobb**. 
4. När jobbet har slutförts kan du kontrollera att den virtuella datorn visas i Azure-målregionen som förväntat.
5. I **Replikerade objekt** högerklickar du på den virtuella datorn > **Genomför**. Detta avslutar flytten till målregionen. Vänta tills incheckningsjobbet har slutförts.

## <a name="discard-the-resource-in-the-source-region"></a>Ta bort resursen från källregionen 

- Gå till den virtuella datorn.  Klicka på **Inaktivera replikering**.  Detta stoppar processen att kopiera data för den virtuella datorn.  

   > [!IMPORTANT]
   > Det är viktigt att du utför det här steget så att du inte debiteras för ASR-replikering.

Fortsätt med nästa uppsättning steg om du inte har för avsikt att återanvända några av källresurserna.

1. Ta bort alla relevanta nätverksresurser från den källregion som ingick i steg 4 i [Förbereda de virtuella källdatorerna](#prepare-the-source-vms) 
2. Ta bort motsvarande lagringskonto från källregionen.



## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du en virtuell Azure-dator till en annan Azure-region. Nu kan du konfigurera haveriberedskap för de flyttade virtuella datorerna.

> [!div class="nextstepaction"]
> [Konfigurera haveriberedskap efter migrering](azure-to-azure-quickstart.md)
