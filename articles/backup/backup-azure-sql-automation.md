---
title: SQL-databas i Azure VM backup & Restore via PowerShell
description: Säkerhetskopiera och Återställ SQL-databaser på virtuella Azure-datorer med hjälp av Azure Backup och PowerShell.
ms.topic: conceptual
ms.date: 03/15/2019
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 115eef3619f76f83f63c5e69e86393c032e0844e
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172626"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>Säkerhetskopiera och återställa SQL-databaser i virtuella Azure-datorer med PowerShell

Den här artikeln beskriver hur du använder Azure PowerShell för att säkerhetskopiera och återställa en SQL-databas i en virtuell Azure-dator med [Azure Backup](backup-overview.md) Recovery Services-valvet.

Den här artikeln förklarar hur du:

> [!div class="checklist"]
>
> * Konfigurera PowerShell och registrera Azure Recovery Services-providern.
> * Skapa ett Recovery Services-valv.
> * Konfigurera säkerhets kopiering för SQL DB i en virtuell Azure-dator.
> * Kör ett säkerhets kopierings jobb.
> * Återställa en säkerhetskopierad SQL-databas.
> * Övervaka säkerhets kopierings-och återställnings jobb.

## <a name="before-you-start"></a>Innan du börjar

* [Läs mer](backup-azure-recovery-services-vault-overview.md) om Recovery Services-valv.
* Läs om funktionerna för att [säkerhetskopiera SQL-databaser i virtuella Azure-datorer](backup-azure-sql-database.md#before-you-start).
* Granska PowerShell-objektcachen för Recovery Services.

### <a name="recovery-services-object-hierarchy"></a>Recovery Services objektets hierarki

Objektets hierarki sammanfattas i följande diagram.

![Recovery Services objektets hierarki](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Granska referens referensen för [cmdleten](/powershell/module/az.recoveryservices) **AZ. RecoveryServices** i Azure-biblioteket.

### <a name="set-up-and-install"></a>Konfigurera och installera

Konfigurera PowerShell på följande sätt:

1. [Ladda ned den senaste versionen av AZ PowerShell](/powershell/azure/install-az-ps). Den lägsta version som krävs är 1.5.0.

2. Hitta Azure Backup PowerShell-cmdlet: ar med det här kommandot:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Granska alias och cmdlets för Azure Backup och Recovery Services-valvet. Här är ett exempel på vad du kan se. Det är inte en fullständig lista över cmdletar.

    ![Lista över Recovery Services-cmdletar](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Logga in på ditt Azure-konto med **Connect-AzAccount**.
5. På webb sidan som visas uppmanas du att ange dina autentiseringsuppgifter för kontot.

    * Alternativt kan du inkludera dina konto uppgifter som en parameter i cmdleten **Connect-AzAccount** med **-Credential**.
    * Om du är en CSP-partner som arbetar för en klient kan du ange kunden som en klient med hjälp av deras tenantID eller primära domän namn. Ett exempel är **Connect-AzAccount-Tenant** fabrikam.com.

6. Associera den prenumeration som du vill använda med kontot, eftersom ett konto kan ha flera prenumerationer.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Om du använder Azure Backup för första gången ska du använda cmdleten **register-AzResourceProvider** för att registrera Azure Recovery Services-providern med din prenumeration.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Verifiera att providern har registrerats:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. I kommandot utdata kontrollerar du att **RegistrationState** ändras till **registrerad**. Om den inte gör det kör du cmdleten **register-AzResourceProvider** igen.

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

Följ dessa steg om du vill skapa ett Recovery Services-valv.

Recovery Services valvet är en Resource Manager-resurs, så du måste placera den i en resurs grupp. Du kan använda en befintlig resurs grupp, eller så kan du skapa en resurs grupp med cmdleten **New-AzResourceGroup** . När du skapar en resurs grupp anger du namn och plats för resurs gruppen.

1. Ett valv placeras i en resurs grupp. Om du inte har en befintlig resurs grupp skapar du en ny med [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). I det här exemplet skapar vi en ny resurs grupp i regionen USA, västra.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Använd cmdleten [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) för att skapa valvet. Ange samma plats för valvet som användes för resurs gruppen.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Ange vilken typ av redundans som ska användas för valv lagringen.

    * Du kan använda [Lokalt Redundant lagring](../storage/common/storage-redundancy-lrs.md) eller [Geo-redundant lagring](../storage/common/storage-redundancy-grs.md).
    * I följande exempel anges alternativet **-BackupStorageRedundancy** för[set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd för **testvault** som är inställt på **interredundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Visa valv i en prenumeration

Om du vill visa alla valv i prenumerationen använder du [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

Utdata ser ut ungefär så här. Den tillhör ande resurs gruppen och platsen tillhandahålls.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Ange valv kontext

Lagra Valve-objektet i en variabel och ange valv kontexten.

* Många Azure Backup-cmdletar kräver att Recovery Services Vault-objektet är inmatat, så det är praktiskt att lagra valvet i en variabel.
* Valvets sammanhang är typen av data som skyddas i valvet. Ange den med [set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). När kontexten har angetts gäller den för alla efterföljande cmdletar.

I följande exempel anges valv kontexten för **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Hämta valv-ID: t

Vi planerar att föråldra inställningen för valv kontext i enlighet med Azure PowerShell rikt linjer. I stället kan du lagra eller hämta valv-ID: t och skicka det till relevanta kommandon enligt följande:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Konfigurera en säkerhetskopieringspolicy

En säkerhets kopierings princip anger schemat för säkerhets kopior och hur länge återställnings punkter för säkerhets kopiering ska behållas:

* En säkerhets kopierings princip är associerad med minst en bevarande princip. En bevarande princip definierar hur länge en återställnings punkt ska sparas innan den tas bort.
* Visa standard kvarhållning av säkerhets kopierings princip med [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Visa standard schema för säkerhets kopiering med [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Du använder cmdleten [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) för att skapa en ny säkerhets kopierings princip. Du har angett schema-och bevarande princip objekt.

Som standard definieras en start tid i objektet Schemalägg princip. Använd följande exempel för att ändra start tiden till önskad start tid. Den önskade start tiden ska också vara i UTC-tid. I exemplet nedan förutsätter du att den önskade start tiden är 01:00 AM UTC för dagliga säkerhets kopieringar.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Du behöver bara ange start tiden i 30 minuter. I ovanstående exempel kan det bara vara "01:00:00" eller "02:30:00". Start tiden får inte vara "01:15:00"

I följande exempel lagras schema principen och bevarande principen i variabler. Den använder sedan variablerna som parametrar för en ny princip (**NewSQLPolicy**). **NewSQLPolicy** tar en daglig "fullständig" säkerhets kopiering, behåller den i 180 dagar och tar en logg säkerhets kopia var 2: e timme

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Utdata ser ut ungefär så här.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>Aktivera säkerhets kopiering

### <a name="registering-the-sql-vm"></a>Registrerar den virtuella SQL-datorn

För virtuella Azure-säkerhetskopieringar och Azure-filresurser kan säkerhets kopierings tjänsten ansluta till dessa Azure Resource Manager resurser och hämta relevant information. Eftersom SQL är ett program i en virtuell Azure-dator måste säkerhets kopierings tjänsten ha behörighet att komma åt programmet och hämta nödvändig information. För att göra det måste du *Registrera* den virtuella Azure-datorn som innehåller SQL-programmet med ett Recovery Services-valv. När du registrerar en virtuell SQL-dator med ett valv kan du bara skydda SQL-databaser till det valvet. Använd [register-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS-cmdlet för att registrera den virtuella datorn.

````powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
````

Kommandot returnerar en "säkerhets kopierings behållare" för den här resursen och statusen är "registrerad"

> [!NOTE]
> Om Force-parametern inte anges uppmanas användaren att bekräfta med en text. vill du inaktivera skyddet för den här behållaren. Ignorera den här texten och säg "Y" för att bekräfta. Detta är ett känt problem och vi arbetar för att ta bort texten och kravet för Force-parametern.

### <a name="fetching-sql-dbs"></a>Hämtar SQL-databaser

När registreringen är färdig kommer säkerhets kopierings tjänsten att kunna visa alla tillgängliga SQL-komponenter på den virtuella datorn. Om du vill visa alla SQL-komponenter som ännu inte har säkerhetskopierats till det här valvet använder du [Get-AzRecoveryServicesBackupProtectableItem PS-](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) cmdleten

````powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
````

Utdata visar alla oskyddade SQL-komponenter i alla virtuella SQL-datorer som är registrerade för det här valvet med objekt typ och ServerName. Du kan filtrera efter en viss virtuell SQL-dator genom att skicka parametern "-container" eller använda kombinationen av "name" och "ServerName" tillsammans med egenskapen ItemType för att komma till ett unikt SQL-objekt.

````powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
````

### <a name="configuring-backup"></a>Konfigurerar säkerhets kopiering

Nu när vi har den SQL-databas som krävs och principen som den måste säkerhets kopie ras till kan vi använda cmdleten [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) för att konfigurera säkerhets kopiering för den här SQL-databasen.

````powershell
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
````

Kommandot väntar tills den konfigurerade säkerhets kopieringen har slutförts och returnerar följande utdata.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Hämtar nya SQL-databaser

När datorn har registrerats kommer säkerhets kopierings tjänsten att hämta information om den databaser som är tillgänglig. Om användaren lägger till SQL-databaser/SQL-instanser till den registrerade datorn senare måste du manuellt aktivera säkerhets kopierings tjänsten för att kunna utföra en ny "förfrågan" för att hämta alla oskyddade databaser (inklusive de nyligen tillagda) igen. Använd cmdleten [Initialize-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS på den virtuella SQL-datorn för att utföra en ny förfrågan. Kommandot väntar tills åtgärden har slutförts. Senare använder du cmdleten [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS för att hämta listan över senaste OSKYDDade SQL-komponenter

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
````

När de relevanta skydds bara objekten har hämtats aktiverar du säkerhets kopieringarna enligt anvisningarna i [avsnittet ovan](#configuring-backup).
Om det inte vill att nya databaser ska identifieras manuellt kan de välja automatisk skydd enligt beskrivningen [nedan](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Aktivera autoskydd

En användare kan konfigurera säkerhets kopiering så att alla databaser som läggs till i framtiden skyddas automatiskt med en viss princip. Använd [Enable-AzRecoveryServicesBackupAutoProtection PS-](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) cmdlet för att aktivera autoskydd.

Eftersom instruktionen är att säkerhetskopiera alla framtida databaser görs åtgärden på en SQLInstance nivå.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

När avsikten med autoskydd har angetts sker förfrågan om att hämta nyligen tillagda databaser i en schemalagd bakgrunds aktivitet var åttonde timme.

## <a name="restore-sql-dbs"></a>Återställ SQL-databaser

Azure Backup kan återställa SQL Server databaser som körs på virtuella Azure-datorer på följande sätt:

* Återställ till ett visst datum eller en angiven tidpunkt (till den andra) med hjälp av säkerhets kopior av transaktions loggen. Azure Backup identifierar automatiskt rätt fullständig differentiell säkerhets kopiering och kedja av logg säkerhets kopior som krävs för att återställa baserat på den valda tiden.
* Återställ en viss fullständig eller differentiell säkerhets kopia för att återställa till en viss återställnings punkt.

Kontrol lera de krav som anges [här](restore-sql-database-azure-vm.md#prerequisites) innan du återställer SQL-databaser.

Börja med att hämta relevant säkerhetskopierad SQL DB med hjälp av [Get-AzRecoveryServicesBackupItem PS-](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) cmdleten.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
````

### <a name="fetch-the-relevant-restore-time"></a>Hämta relevant återställnings tid

Som beskrivs ovan kan användaren återställa den säkerhetskopierade SQL-databasen till en fullständig/differentiell kopia **eller** till en logg tidpunkt.

#### <a name="fetch-distinct-recovery-points"></a>Hämta distinkta återställnings punkter

Använd [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) för att hämta distinkta (fullständiga/differentiella) återställnings punkter för en säkerhetskopierad SQL-databas.

````powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
````

Utdata liknar följande exempel

````powershell
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
````

Använd filtret ' RecoveryPointId ' eller ett mat ris filter för att hämta relevant återställnings punkt.

````powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
````

#### <a name="fetch-point-in-time-recovery-point"></a>Hämtnings punkt-tidpunkt för återställning

Om användaren vill återställa databasen till en viss tidpunkt, använder du [Get-AzRecoveryServicesBackupRecoveryLogChain PS-](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) cmdlet. Cmdleten returnerar en lista med datum som representerar start-och slut tider för en bruten, kontinuerlig logg kedja för det SQL-säkerhetskopierade objektet. Den önskade tidpunkten ska ligga inom det här intervallet.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

Utdata ser ut ungefär som i följande exempel.

````powershell
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
````

Ovanstående utdata innebär att användaren kan återställa till valfri tidpunkt mellan den visade start tiden och slut tiden. Tiderna är UTC. Konstruera alla tidpunkter i PS som ligger inom det intervall som visas ovan.

> [!NOTE]
> När en loggnings punkt aktive ras för återställning behöver användaren inte ange start punkten, d.v.s. den fullständiga säkerhets kopian som databasen återställs från. Azure Backups tjänsten tar hand om hela återställnings planen, t. ex. vilken fullständig säkerhets kopia du väljer, vilken logg säkerhets kopia som ska användas osv.

### <a name="determine-recovery-configuration"></a>Ange återställnings konfiguration

I händelse av SQL DB-återställning stöds följande återställnings scenarier.

* Åsidosätta den säkerhetskopierade SQL-databasen med data från en annan återställnings punkt – OriginalWorkloadRestore
* Återställa SQL DB som en ny databas i samma SQL-instans – AlternateWorkloadRestore
* Återställa SQL DB som en ny databas i en annan SQL-instans i en annan virtuell SQL-AlternateWorkloadRestore

När du har hämtat relevant återställnings punkt (distinkt eller logg tidpunkt) använder du [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig PS-](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) cmdlet: en för att hämta återställnings konfigurations objekt enligt önskad återställnings plan.

#### <a name="original-workload-restore"></a>Återställning av ursprunglig arbets belastning

Om du vill åsidosätta den säkerhetskopierade databasen med data från återställnings punkten anger du bara rätt flagga och lämplig återställnings punkt enligt följande exempel.

##### <a name="original-restore-with-distinct-recovery-point"></a>Ursprunglig återställning med en distinkt återställnings punkt

````powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="original-restore-with-log-point-in-time"></a>Ursprunglig återställning med logg tidpunkt

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Alternativ återställning av arbets belastning

> [!IMPORTANT]
> En säkerhetskopierad SQL-databas kan återställas som en ny databas till en annan SQLInstance, i en registrerad virtuell Azure-dator i det här valvet.

Som beskrivs ovan, om mål-SQLInstance ligger inom en annan virtuell Azure-dator, kontrollerar du att den är [registrerad på det här valvet](#registering-the-sql-vm) och att den relevanta SQLInstance visas som ett skydds objekt.

````powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
````

Sedan skickar du bara den relevanta återställnings punkten, mål-SQL-instansen med rätt flagga som visas nedan.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Alternativ återställning med distinkt återställnings punkt

````powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="alternate-restore-with-log-point-in-time"></a>Alternativ återställning med inloggnings tidpunkt

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

Det sista konfigurations objekt för återställnings punkten som hämtades från [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS-cmdlet: en innehåller all relevant information för återställning och ser nedan.

````powershell
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
````

Du kan redigera fälten återställda DB-namn, OverwriteWLIfpresent, NoRecoveryMode och targetPhysicalPath. Få mer information om sökvägar till mål filen enligt nedan.

````powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath

MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
````

Ange de relevanta PS-egenskaperna som sträng värden som visas nedan.

````powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl

TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
````

> [!IMPORTANT]
> Se till att det slutliga återställnings konfigurations objektet har alla nödvändiga och korrekta värden eftersom återställnings åtgärden baseras på konfigurationsobjektet konfiguration.

### <a name="restore-with-relevant-configuration"></a>Återställ med relevant konfiguration

När relevant återställnings konfigurations objekt har hämtats och verifierats använder du [AzRecoveryServicesBackupItem PS-](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) cmdlet: en för att starta återställnings processen.

````powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
````

Restore-åtgärden returnerar ett jobb som ska spåras.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
````

## <a name="manage-sql-backups"></a>Hantera SQL-säkerhetskopieringar

### <a name="on-demand-backup"></a>Säkerhets kopiering på begäran

När säkerhets kopiering har Aktiver ATS för en databas kan användaren även utlösa en säkerhets kopiering på begäran för databasen med [Backup-AzRecoveryServicesBackupItem PS-](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) cmdlet. I följande exempel utlöses en fullständig säkerhets kopiering på en SQL DB med komprimering aktive rad och den fullständiga säkerhets kopieringen ska behållas i 60 dagar.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
````

Kommandot för säkerhets kopiering på begäran returnerar ett jobb som ska spåras.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
````

Om utdata förloras eller om du vill hämta det relevanta jobb-ID: t [hämtar du listan över jobb](#track-azure-backup-jobs) från Azure Backup tjänsten och spårar den och dess information.

### <a name="change-policy-for-backup-items"></a>Ändra princip för säkerhets kopierings objekt

Användaren kan antingen ändra den befintliga principen eller ändra principen för det säkerhetskopierade objektet från Policy1 till Policy2. Om du vill växla principer för ett säkerhetskopierat objekt hämtar du den relevanta principen och säkerhetskopierar objektet och använder kommandot [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) med säkerhets kopierings objekt som parameter.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

Kommandot väntar tills den konfigurerade säkerhets kopieringen har slutförts och returnerar följande utdata.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>Registrera virtuella SQL-datorer på nytt

> [!WARNING]
> Se till att läsa det här [dokumentet](backup-sql-server-azure-troubleshoot.md#re-registration-failures) för att förstå fel symptomen och orsakerna till detta innan du försöker registrera igen

Om du vill utlösa omregistreringen av den virtuella SQL-datorn hämtar du den relevanta säkerhets kopierings behållaren och skickar den till register-cmdleten.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
````

### <a name="stop-protection"></a>Stoppa skydd

#### <a name="retain-data"></a>Behåll data

Om användaren vill stoppa skyddet kan de använda cmdleten [disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS. Detta stoppar schemalagda säkerhets kopieringar men data som säkerhets kopie ras tills nu kvarhålls för alltid.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Ta bort säkerhetskopieringsdata

För att fullständigt ta bort lagrade säkerhets kopierings data i valvet lägger du bara till flaggan-RemoveRecoveryPoints eller växlar till [skydds kommandot Disable (inaktivera)](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

#### <a name="disable-auto-protection"></a>Inaktivera automatiskt skydd

Om Autoprotection konfigurerades på en SQLInstance kan användaren inaktivera den med hjälp av cmdleten [disable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS.

````powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
````

#### <a name="unregister-sql-vm"></a>Avregistrera virtuell SQL-dator

Om alla databaser av en SQL-Server [inte längre är skyddade och det inte finns några säkerhets kopierings data](#delete-backup-data), kan användaren avregistrera den virtuella SQL-datorn från det här valvet. Användaren kan bara skydda databaser till ett annat valv. Använd [unregister-AzRecoveryServicesBackupContainer PS-](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) cmdlet för att avregistrera den virtuella SQL-datorn.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
````

### <a name="track-azure-backup-jobs"></a>Spåra Azure Backup jobb

Det är viktigt att Observera att Azure Backup bara spårar användare som har utlöst jobb i SQL-säkerhetskopiering. Schemalagda säkerhets kopieringar (inklusive logg säkerhets kopior) syns inte i portalen/PowerShell. Men om det inte går att utföra schemalagda jobb genereras en [säkerhets kopierings avisering](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) och visas i portalen. [Använd Azure Monitor](backup-azure-monitoring-use-azuremonitor.md) för att spåra alla schemalagda jobb och annan relevant information.

Användare kan spåra åtgärder på begäran/användare som utlöses med den JobID som returneras i [resultatet](#on-demand-backup) av asynkrona jobb som säkerhets kopiering. Använd [Get-AzRecoveryServicesBackupJobDetail PS-](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) cmdlet: en för att spåra jobbet och dess information.

````powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
````

Använd [Get-AzRecoveryServicesBackupJob PS-](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) cmdleten om du vill hämta en lista över jobb på begäran och deras status från Azure Backup tjänsten. I följande exempel returneras alla pågående SQL-jobb.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Om du vill avbryta ett pågående jobb använder du cmdleten [Stop-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS.

## <a name="managing-sql-always-on-availability-groups"></a>Hantera tillgänglighets grupper för SQL Always on

För SQL Always on-tillgänglighetsgrupper, se till att [registrera alla noder](#registering-the-sql-vm) i tillgänglighets gruppen (AG). När registreringen är färdig för alla noder skapas ett SQL-tillgänglighetsgrupper för tillgänglighets gruppen logiskt under objekt som kan skyddas. Databaserna under SQL-AG visas som "SQLDatabase". Noderna visas som fristående instanser och standard-SQL-databaserna under dem visas även som SQL-databaser.

Anta till exempel att en SQL AG har två noder: SQL-Server-0 och SQL-Server-1 och 1 SQL AG DB. När båda dessa noder är registrerade, om användaren [visar en lista över de objekt](#fetching-sql-dbs)som kan skyddas, visas följande komponenter

* En SQL AG-objekts skydds bara objekt typ som SQLAvailabilityGroup
* En SQL AG DB-skyddad objekt typ som SQLDatabase
* SQL-Server-0-skydds bara objekt typ som SQLInstance
* SQL-Server-1-skydds bara objekt typ som SQLInstance
* Alla standard-SQL-databaser (Master, Model, msdb) under SQL-Server-0-skydds bara objekt typ som SQLDatabase
* Alla standard-SQL-databaser (Master, Model, msdb) under SQL-Server-1-skydds bara objekt typ som SQLDatabase

SQL-Server-0, SQL-Server-1 visas också som "AzureVMAppContainer" när [säkerhets kopierings behållarna visas](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0).

Hämta bara den relevanta SQL-databasen för att [Aktivera säkerhets](#configuring-backup) kopiering och [säkerhets kopiering på begäran](#on-demand-backup) och [Återställ PS-cmdletar](#restore-sql-dbs) är identiska.
