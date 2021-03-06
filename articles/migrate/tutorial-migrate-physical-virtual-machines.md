---
title: Migrera lokala fysiska datorer eller virtualiserade datorer till Azure med Azure Migrate Server-migrering | Microsoft Docs
description: I den här artikeln beskrivs hur du migrerar lokala fysiska datorer eller virtualiserade datorer till Azure med Azure Migrate Server-migrering.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 28705ea8a552f4d2e6653857c69ebb8d5f87b962
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907121"
---
# <a name="migrate-physical-or-virtualized-servers-to-azure"></a>Migrera fysiska eller virtualiserade servrar till Azure 

Den här artikeln visar hur du migrerar fysiska eller virtualiserade servrar till Azure. Migreringsverktyg för Azure Migrate Server erbjuder migrering av fysiska och virtualiserade servrar, med hjälp av en agent-baserad replikering. Med det här verktyget kan du migrera en mängd olika datorer till Azure:

- Migrera lokala fysiska servrar.
- Migrera virtuella datorer som är virtualiserade med plattformar som xen, KVM.
- Migrera virtuella Hyper-V-eller VMware-datorer. Detta är användbart om du av någon anledning inte kan använda det standardmigrerings flöde som Azure Migrate Server migration för [Hyper-V](tutorial-migrate-hyper-v.md), [VMware agent](tutorial-migrate-vmware.md) lös migrering eller [VMware-agent-baserad](tutorial-migrate-vmware-agent.md) migrering.
- Migrera virtuella datorer som körs i privata moln.
- Migrera virtuella datorer som körs i offentliga moln, till exempel Amazon Web Services (AWS) eller Google Cloud Platform (GCP).


[Azure Migrate](migrate-services-overview.md) tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av dina lokala appar och arbets belastningar och virtuella dator instanser i molnet till Azure. Hubben innehåller Azure Migrate verktyg för utvärdering och migrering samt oberoende program varu leverantörer från tredje part (ISV).


I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Förbered Azure för migrering med verktyget för migrering av Azure Migrate Server.
> * Kontrol lera kraven för datorer som du vill migrera och Förbered en dator för Azure Migrate Replication-enheten som används för att identifiera och migrera datorer till Azure.
> * Lägg till Migreringsverktyg för Azure Migrate server i Azure Migrate Hub.
> * Konfigurera replikerings enheten.
> * Installera mobilitets tjänsten på datorer som du vill migrera.
> * Aktivera replikering.
> * Kör en testmigrering för att se till att allt fungerar som förväntat.
> * Kör en fullständig migrering till Azure.

> [!NOTE]
> Självstudier visar dig den enklaste distributions Sök vägen för ett scenario så att du snabbt kan konfigurera ett koncept för koncept bevis. Självstudier använder standard alternativ där det är möjligt, och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i instruktionen how-TOS för Azure Migrate.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prerequisites"></a>Krav

Innan du börjar de här självstudierna bör du:

1. [Granska](migrate-architecture.md) migreringens arkitektur.
2. Se till att ditt Azure-konto har tilldelats rollen virtuell dator deltagare, så att du har behörighet att:

    - Skapa en virtuell dator i den valda resursgruppen.
    - Skapa en virtuell dator i det valda virtuella nätverket.
    - Skriv till en Azure-hanterad disk. 

3. [Konfigurera ett Azure-nätverk](../virtual-network/manage-virtual-network.md#create-a-virtual-network). När du replikerar till Azure skapas virtuella Azure-datorer och ansluts till ett Azure-nätverk som du anger när du konfigurerar migrering.


## <a name="prepare-azure"></a>Förbereda Azure

Konfigurera Azure-behörigheter innan du kan migrera med Azure Migrate Server-migrering.

- **Skapa ett projekt**: ditt Azure-konto måste ha behörighet att skapa ett Azure Migrate-projekt. 
- **Registrera Azure Migrate Replication**-enhet: replikeringstjänsten skapar och registrerar en Azure Active Directory-app på ditt Azure-konto. Delegera behörigheter för detta.
- **Skapa Key Vault**: om du vill migrera datorer skapar Azure Migrate en Key Vault i resurs gruppen för att hantera åtkomst nycklar till replikeringens lagrings konto i din prenumeration. Om du vill skapa valvet behöver du Roll tilldelnings behörigheter för resurs gruppen där Azure Migrate-projektet finns. 


### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter för att skapa projekt

1. Öppna prenumerationen i Azure Portal och välj **åtkomst kontroll (IAM)** .
2. Leta upp det relevanta kontot i **kontrol lera åtkomst**och klicka på det för att visa behörigheter.
3. Du bör ha behörighet som **deltagare** eller **ägare** .
    - Om du precis har skapat ett kostnads fritt Azure-konto är du ägare till din prenumeration.
    - Om du inte är prenumerations ägare kan du samar beta med ägaren för att tilldela rollen.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Tilldela behörigheter för att registrera replikerings enheten

För agent-baserad migrering delegerar du behörigheter för Azure Migrate Server-migrering för att skapa och registrera en Azure AD-App i ditt konto. Du kan tilldela behörigheter med någon av följande metoder:

- En klient/global-administratör kan bevilja behörigheter till användare i klienten, för att skapa och registrera Azure AD-appar.
- En klient/global administratör kan tilldela rollen programutvecklare (som har behörighet) till kontot.

Det är värt att notera att:

- Apparna har inte någon annan åtkomst behörighet för prenumerationen förutom de som beskrivs ovan.
- Du behöver bara dessa behörigheter när du registrerar en ny replikeringsprincip. Du kan ta bort behörigheterna när replikeringsprincipen har kon figurer ATS. 


#### <a name="grant-account-permissions"></a>Bevilja konto behörigheter

Klient organisationen/den globala administratören kan bevilja behörigheter enligt följande

1. I Azure AD bör klient organisationen/den globala administratören navigera till **Azure Active Directory** > **användare** > **användar inställningar**.
2. Administratören bör ange **Appregistreringar** till **Ja**.

    ![Azure AD-behörigheter](./media/tutorial-migrate-physical-virtual-machines/aad.png)

> [!NOTE]
> Detta är en standardinställning som inte är känslig. [Läs mer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Tilldela rollen program utvecklare 

Klient organisationen/den globala administratören kan tilldela rollen programutvecklare till ett konto. [Läs mer](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="assign-permissions-to-create-key-vault"></a>Tilldela behörigheter för att skapa Key Vault

Tilldela roll tilldelnings behörigheter för resurs gruppen där Azure Migrate-projektet finns, enligt följande:

1. I resurs gruppen i Azure Portal väljer du **åtkomst kontroll (IAM)** .
2. Leta upp det relevanta kontot i **kontrol lera åtkomst**och klicka på det för att visa behörigheter. Du behöver behörighet som **ägare** (eller **deltagare** och **användar åtkomst administratör**).
3. Om du inte har de behörigheter som krävs kan du begära dem från resurs gruppens ägare. 

## <a name="prepare-for-migration"></a>Förbereda för migrering

### <a name="check-machine-requirements-for-migration"></a>Kontrol lera dator kraven för migrering

Kontrol lera att datorerna uppfyller kraven för migrering till Azure. 

> [!NOTE]
> Agent-baserad migrering med Azure Migrate Server-migrering baseras på funktionerna i tjänsten Azure Site Recovery. Vissa krav kan vara länkade till Site Recovery-dokumentationen.

1. [Kontrol lera](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) Server kraven.
2. [Verifiera](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) Krav för VM-support för migrering.
3. Verifiera inställningarna för virtuella datorer. Lokala virtuella datorer som du replikerar till Azure måste uppfylla kraven för [virtuella Azure-datorer](migrate-support-matrix-vmware.md#azure-vm-requirements).


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Förbereda en dator för replikerings enheten

Azure Migrate Server-migrering använder en replikeringsfil för att replikera datorer till Azure. Replikeringssystemet kör följande komponenter.

- **Konfigurations**Server: konfigurations servern samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering.
- **Processerver**: processervern fungerar som en gateway för replikering. Den tar emot replikeringsdata; optimerar den med cachelagring, komprimering och kryptering och skickar den till ett cache Storage-konto i Azure. 

Innan du börjar måste du förbereda en Windows Server 2016-dator för att vara värd för replikerings enheten. Datorn bör uppfylla [dessa krav](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements). Enheten bör inte installeras på en käll dator som du vill skydda.


## <a name="add-the-azure-migrate-server-migration-tool"></a>Lägg till verktyget för migrering av Azure Migrate Server

Konfigurera ett Azure Migrate projekt och Lägg sedan till verktyget för migrering av Azure Migrate Server.

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
2. Under **Tjänster** väljer du **Azure Migrate**.
3. I **översikten** klickar du på **Utvärdera och migrera servrar**.
4. Under **identifiera, utvärdera och migrera servrar**klickar du på **utvärdera och migrera servrar**.

    ![Identifiera och utvärdera servrar](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. I **Discover, assess and migrate servers** (Identifiera, utvärdera och migrera servrar) klickar du på **Lägg till verktyg**.
6. I **Migrera projekt** väljer du din Azure-prenumeration och skapar en resursgrupp om du inte har någon.
7. I **projekt information**anger du det projekt namn och geografi som du vill skapa projektet i och klickar på **Nästa**

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

    Du kan skapa ett Azure Migrate-projekt i någon av dessa geografiska områden.

    **Geografi** | **Region**
    --- | ---
    Asien | Sydostasien
    Europa | Europa, norra eller Europa, västra
    USA | USA, östra eller USA, västra centrala

    Den angivna geografiska platsen för projektet används bara för att lagra de metadata som samlats in från lokala virtuella datorer. Du kan välja valfri mål region för den faktiska migreringen.
8. I **Välj bedömnings verktyg**väljer du **hoppa över Lägg till ett utvärderings verktyg för tillfället** > **Nästa**.
9. I **Välj Migreringsverktyg**väljer du **Azure Migrate: Server Migration** > **Nästa**.
10. I **Review + add tools** (Granska + lägg till verktyg)
granskar du inställningarna och klickar på **Lägg till verktyg**
11. När du har lagt till verktyget visas det i Azure Migrate Project >- **servrar** > **Migreringsverktyg**.

## <a name="set-up-the-replication-appliance"></a>Konfigurera replikerings enheten

Det första steget i migreringen är att konfigurera replikerings enheten. Du kan hämta installations filen för installationen och köra den på den [dator som du](#prepare-a-machine-for-the-replication-appliance)har för berett. När du har installerat installationen registrerar du den med Azure Migrate Server-migrering.


### <a name="download-the-replication-appliance-installer"></a>Ladda ned installations programmet för replikerings enheten

1. Klicka på **identifiera**i ***Azure Migrate: Server-migrering**i Azure Migrate Project >- **servrar**.

    ![Identifiera virtuella datorer](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. I **identifiera datorer** > **dina datorer virtualiserade?** , klicka på **inte virtualiserad/annan**.
4. I **mål region**väljer du den Azure-region som du vill migrera datorerna till.
5. Välj **Bekräfta att mål regionen för migrering är regions namn**.
6. Klicka på **Skapa resurser**. Detta skapar ett Azure Site Recovery valv i bakgrunden.
    - Om du redan har konfigurerat migrering med Azure Migrate Server-migreringen kan du inte konfigurera mål alternativet eftersom resurserna tidigare har kon figurer ATS.
    - Du kan inte ändra mål region för projektet när du har klickat på den här knappen.
    - Alla efterföljande migreringar är till den här regionen.

7. I vill **du installera en ny replikeringsprincip? väljer du** **installera en replikeringsprincip**.
9. I **Hämta och installera installations programmet för replikering**laddar du ned installations programmet för installationen och registrerings nyckeln. Du behöver nyckeln för att kunna registrera installationen. Nyckeln är giltig i fem dagar efter att den har laddats ned.

    ![Hämta Provider](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Kopiera installations filen för installationen och nyckel filen till Windows Server 2016-datorn som du skapade för installationen.
11. Kör installations filen för replikeringstjänsten enligt beskrivningen i nästa procedur.
12. När installationen har startats om efter installationen går du till **identifiera datorer**, väljer den nya installationen i **Välj konfigurations Server**och klickar på **Slutför registrering**. Genom att slutföra registreringen utförs några slutliga uppgifter för att förbereda replikeringen.

    ![Slutför registrering](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Det kan ta upp till 15 minuter innan registreringen har slutförts tills identifierade datorer visas i Azure Migrate Server-migrering. När virtuella datorer identifieras ökar antalet **identifierade servrar** .

![Identifierade servrar](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>Installera mobilitetstjänsten

På datorer som du vill migrera måste du installera mobilitets tjänst agenten. Agent installationerna är tillgängliga på replikerings enheten. Du hittar rätt installations program och installerar agenten på varje dator som du vill migrera. Gör detta på följande sätt:

1. Logga in på replikerings enheten.
2. Navigera till **%programdata%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Hitta installations programmet för datorns operativ system och version. Granska [operativ system som stöds](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines). 
4. Kopiera installations filen till den dator som du vill migrera.
5. Kontrol lera att du har den lösen fras som genererades när du distribuerade enheten.
    - Lagra filen i en tillfällig textfil på datorn.
    - Du kan hämta lösen frasen på replikerings enheten. Från kommando raden kör du **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe-v** för att visa den aktuella lösen frasen.
    - Återskapa inte lösen frasen. Detta bryter anslutningen och du måste registrera om replikerings enheten.


### <a name="install-on-windows"></a>Installera i Windows

1. Extrahera innehållet i installations filen till en lokal mapp (till exempel C:\Temp) på datorn enligt följande:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Kör installations programmet för mobilitets tjänsten:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```
3. Registrera agenten med replikerings enheten:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Installera på Linux

1. Extrahera innehållet i installations tarball till en lokal mapp (till exempel/tmp/MobSvcInstaller) på datorn enligt följande:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Kör installations skriptet:
    ```
    sudo ./install -r MS -q
    ```
3. Registrera agenten med replikerings enheten:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Replikera datorer

Välj datorer för migrering nu. 

> [!NOTE]
> Du kan replikera upp till 10 datorer tillsammans. Om du behöver replikera mer replikerar du dem samtidigt i batchar med 10.

1. I Azure Migrate Project >- **servrar** **Azure Migrate: Server-migrering**klickar du på **Replikera**.

    ![Replikera virtuella datorer](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. I **Replikera**, > **käll inställningar** > **att datorerna har virtualiserats?** väljer du **inte virtualiserad/övrigt**.
3. I **lokal**installation väljer du namnet på Azure Migrate-installationen som du konfigurerar.
4. I **vCenter Server**anger du namnet på den vCenter-Server som hanterar de virtuella datorerna eller den vSphere-server som de virtuella datorerna finns på.
5. I **processerver**väljer du namnet på replikerings enheten.
6. I **autentiseringsuppgifter för gäst**anger du ett administratörs konto för den virtuella datorn som ska användas för push-installation av mobilitets tjänsten. I den här självstudien installerar vi mobilitets tjänsten manuellt, så du kan lägga till alla dummy-konton. Klicka sedan på **Nästa: virtuella datorer**.

    ![Replikera virtuella datorer](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. I **Virtual Machines**, i **Importera migreringsjobb från en utvärdering?** , lämnar du standardinställningen **Nej, jag anger inställningarna för migrering manuellt**.
8. Markera varje virtuell dator som du vill migrera. Klicka sedan på **Nästa: mål inställningar**.

    ![Välj virtuella datorer](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. I **Målinställningar** väljer du prenumeration och den målregion som du vill migrera till. Ange sedan den resursgrupp där du vill att de virtuella Azure-datorerna ska finnas efter migreringen.
10. I **Virtuellt nätverk** väljer du det Azure VNet/undernät som de virtuella Azure-datorerna ska anslutas till efter migreringen.
11. I **Azure Hybrid-förmån**:

    - Välj **Nej** om du inte vill använda Azure Hybrid-förmånen. Klicka sedan på **Nästa**.
    - Välj **Ja** om du har Windows Server-datorer som omfattas av aktiva Software Assurance- eller Windows Server-prenumerationer och du vill tillämpa förmånen på de datorer som du migrerar. Klicka sedan på **Nästa**.

    ![Mål inställningar](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. I **Compute** granskar du namnet på den virtuella datorn, storlek, disktyp för operativsystemet och tillgänglighetsuppsättningen. De virtuella datorerna måste följa [Azures krav](migrate-support-matrix-vmware.md#azure-vm-requirements).

    - **VM-storlek**: Azure Migrate Server-migreringen väljer som standard en storlek baserat på den närmaste matchningen i Azure-prenumerationen. Du kan också välja en storlek manuellt i **Storlek på virtuell Azure-dator**. 
    - **OS-disk**: Ange OS-disken (start) för den virtuella datorn. Operativsystemdisken är den disk där operativsystemets bootloader och installationsprogram finns. 
    - **Tillgänglighets uppsättning**: om den virtuella datorn ska finnas i en Azure-tillgänglighets uppsättning efter migreringen anger du uppsättningen. Uppsättningen måste finnas i målets resursgrupp som du anger för migreringen.

    ![Beräknings inställningar](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. I **diskar**anger du om de virtuella dator diskarna ska replikeras till Azure och väljer disk typ (standard SSD/HDD eller Premium Managed Disks) i Azure. Klicka sedan på **Nästa**.
    - Du kan undanta diskar från replikering.
    - Om du undantar diskar kommer de inte att synas i den virtuella Azure-datorn efter migreringen. 

    ![Disk inställningar](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. I **Granska och starta replikering** kontrollerar du inställningarna och klickar på **Replikera** för att påbörja den första replikeringen för servrarna.

> [!NOTE]
> Du kan uppdatera replikeringsinställningar varje tid innan replikeringen startar, **hantera** > **Replikera datorer**. Det går inte att ändra inställningarna efter att replikeringen har startat.



## <a name="track-and-monitor"></a>Spåra och övervaka

- När du klickar på **Replikera** startar du jobbet starta replikering. 
- När jobbet starta replikeringen har slutförts påbörjar datorerna sin inledande replikering till Azure.
- När den inledande replikeringen har slutförts börjar delta-replikeringen. Stegvisa ändringar av lokala diskar replikeras regelbundet till replik diskarna i Azure.


Du kan spåra jobb status i Portal meddelanden.

Du kan övervaka replikeringsstatus genom att klicka på **Replikera servrar** i **Azure Migrate: Server-migrering**.
![övervaka replikering](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Kör en testmigrering


När delta-replikering börjar kan du köra en testmigrering för de virtuella datorerna innan du kör en fullständig migrering till Azure. Vi rekommenderar starkt att du gör detta minst en gång för varje dator innan du migrerar den.

- Genom att köra en test-migrering kontrollerar du att migreringen fungerar som förväntat, utan att det påverkar de lokala datorerna, som fortsätter att fungera och fortsätter att replikeras. 
- Testmigrering simulerar migreringen genom att skapa en virtuell Azure-dator med replikerade data (vanligt vis migrera till ett virtuellt nätverk som inte är i Azure-prenumerationen).
- Du kan använda den replikerade virtuella Azure-datorn för att verifiera migreringen, utföra app-testning och åtgärda eventuella problem före fullständig migrering.

Gör en testmigrering enligt följande:


1. I **mål för migrering** > **servrar** > **Azure Migrate: Server migrering**klickar du på **test migrerade servrar**.

     ![Testmigrerade servrar](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Högerklicka på den virtuella dator som ska testas och klicka på **Testmigrera**.

    ![Testmigrera](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. I **Testmigrera** väljer du det Azure VNet där den virtuella Azure-datorn kommer att finnas efter migreringen. Vi rekommenderar att du använder ett VNet för icke-produktion.
4. **Testmigreringen** startas. Övervaka jobbet i portalmeddelanden.
5. När migreringen är klar kan du se den migrerade virtuella Azure-datorn i **Virtual Machines** i Azure Portal. Datornamnet har suffixet **-Test**.
6. När testet är klart högerklickar du på den virtuella Azure-datorn i **Replikera datorer** och klickar på **Rensa upp i testmigreringen**.

    ![Rensa migrering](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Migrera virtuella datorer

När du har kontrollerat att testmigreringen fungerar som förväntat kan du migrera de lokala datorerna.

1. I Azure Migrate Project >- **servrar** > **Azure Migrate: Server-migrering**klickar du på **Replikera servrar**.

    ![Servrarna replikeras](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. I **Replikera datorer** högerklickar du på den virtuella datorn > **Migrera**.
3. I **Migrera** > **Stäng av virtuella datorer och utför en planerad migrering utan dataförlust** väljer du **Ja** > **OK**.
    - Som standard stänger Azure Migrate av den lokala virtuella datorn och kör en replikering på begäran som synkroniserar eventuella VM-ändringar som har inträffat sedan den senaste replikeringen utfördes. Detta säkerställer att ingen dataförlust sker.
    - Om du inte vill stänga av den virtuella datorn väljer du **Nej**
4. Ett migreringsjobb startas för den virtuella datorn. Spåra jobbet i Azure-meddelanden.
5. När jobbet är klart kan du se och hantera den virtuella datorn på sidan **Virtual Machines**.

## <a name="complete-the-migration"></a>Slutföra migreringen

1. När migreringen är färdig högerklickar du på den virtuella datorn > **stoppa migreringen**. Detta stoppar replikeringen för den lokala datorn och rensar information om replikeringstillståndet för den virtuella datorn.
2. Installera [Azure VM-](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) eller [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) -agenten på de migrerade datorerna.
3. Utför alla finjusteringar av appen efter migreringen som att uppdatera databasanslutningssträngar och webbserverkonfigurationer.
4. Utför slutlig program- och migreringsacceptanstestning på det migrerade programmet som nu körs i Azure.
5. Klipp ut över trafik till den migrerade virtuella Azure-instansen.
6. Ta bort de lokala virtuella datorerna från ditt lokala VM-inventarie.
7. Ta bort de lokala virtuella datorerna från lokala säkerhetskopior.
8. Uppdatera eventuell intern dokumentation för att ange den nya platsen och IP-adressen för de virtuella Azure-datorerna. 

## <a name="post-migration-best-practices"></a>Metod tips för efter migreringen

- För ökat skydd:
    - Skydda data genom att säkerhetskopiera virtuella Azure-datorer med Azure Backup-tjänsten. [Läs mer](../backup/quick-backup-vm-portal.md).
    - Håll arbetsbelastningar i körning och kontinuerligt tillgängliga genom att replikera virtuella Azure-datorer till en sekundär region med Site Recovery. [Läs mer](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- För ökad säkerhet:
    - Lås och begränsa inkommande trafik åtkomst med [Azure Security Center – just-in-Time-administration](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Begränsa nätverkstrafik till hanteringsslutpunkter med [nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Distribuera [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) för att säkra diskar och skydda data från stöld och obehörig åtkomst.
    - Läs mer om [ att skydda IaaS-resurser](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) och besök [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- För övervakning och hantering:
    - Överväg att distribuera [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) för att övervaka användning och utgifter.


## <a name="next-steps"></a>Nästa steg

Undersök [resan för migrering i molnet](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) i Azure Cloud adoption Framework.
