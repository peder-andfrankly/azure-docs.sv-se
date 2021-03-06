---
title: Haveri beredskap för virtuella Azure-datorer med Azure PowerShell och Azure Site Recovery
description: Lär dig hur du konfigurerar haveri beredskap för virtuella Azure-datorer med Azure Site Recovery som använder Azure PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: aa91725daf36113334849dd15dd01b6ce6ed4389
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621079"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Konfigurera katastrof återställning för virtuella Azure-datorer med hjälp av Azure PowerShell


I den här artikeln får du se hur du ställer in och testar haveri beredskap för virtuella Azure-datorer med hjälp av Azure PowerShell.

Lär dig att:

> [!div class="checklist"]
> - Skapa ett Recovery Services-valv.
> - Ange valv kontexten för PowerShell-sessionen.
> - Förbered valvet för att börja replikera virtuella Azure-datorer.
> - Skapa nätverks mappningar.
> - Skapa lagrings konton för att replikera virtuella datorer till.
> - Replikera virtuella Azure-datorer till ett återställnings område för haveri beredskap.
> - Utför en redundanstest, validera och rensa redundanstest.
> - Redundans till återställnings regionen.

> [!NOTE]
> Alla scenario funktioner som är tillgängliga via portalen kan vara tillgängliga via Azure PowerShell. Några av de scenario funktioner som inte stöds för närvarande finns i Azure PowerShell:
> - Möjlighet att ange att alla diskar på en virtuell dator ska replikeras utan att uttryckligen ange varje disk för den virtuella datorn.  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar:
- Vara säker på att du förstår [arkitekturen och komponenterna för scenariot](azure-to-azure-architecture.md).
- Granska [kraven för stöd](azure-to-azure-support-matrix.md) för alla komponenter.
- Du har modulen Azure PowerShell `Az`. Om du behöver installera eller uppgradera Azure PowerShell, följ den här [guiden för att installera och konfigurera Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Logga in på din Microsoft Azure prenumeration

Logga in på din Azure-prenumeration med cmdleten Connect-AzAccount

```azurepowershell
Connect-AzAccount
```
Välj din Azure-prenumeration. Använd cmdleten Get-AzSubscription för att hämta listan över Azure-prenumerationer som du har åtkomst till. Välj den Azure-prenumeration som du vill arbeta med med hjälp av cmdleten Select-AzSubscription.

```azurepowershell
Select-AzSubscription -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>Hämta information om den eller de virtuella datorerna som ska replikeras

I exemplet i den här artikeln kommer en virtuell dator i regionen USA, östra att replikeras till och återställas i regionen USA, västra 2. Den virtuella datorn replikeras är en virtuell dator med en OS-disk och en enskild datadisk. Namnet på den virtuella datorn som används i exemplet är AzureDemoVM.

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

Hämta disk information för diskarna för den virtuella datorn. Disk information kommer att användas senare när replikeringen för den virtuella datorn startas.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

Skapa en resurs grupp där du kan skapa Recovery Servicess valvet.

> [!IMPORTANT]
> * Recovery Services-valvet och de virtuella datorer som skyddas måste finnas på olika Azure-platser.
> * Resurs gruppen för Recovery Services-valvet och de virtuella datorer som skyddas måste finnas på olika Azure-platser.
> * Recovery Services-valvet och resurs gruppen som den tillhör, kan finnas på samma Azure-plats.

I exemplet i den här artikeln är den virtuella datorn som skyddas i regionen USA, östra. Återställnings regionen som valts för haveri beredskap är regionen USA, västra 2. Recovery Services-valvet och resurs gruppen för valvet är båda i återställnings regionen (västra USA 2)

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```
```
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```

Skapa ett Recovery Services-valv. I exemplet nedan skapas ett Recovery Services valv med namnet a2aDemoRecoveryVault i regionen USA, västra 2.

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```
```
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```
## <a name="set-the-vault-context"></a>Ange valv kontext


Ange valv kontexten som ska användas i PowerShell-sessionen. När du har angett utförs efterföljande Azure Site Recovery åtgärder i PowerShell-sessionen i kontexten för det valda valvet.

 ```azurepowershell
#Setting the vault context.
Set-AsrVaultSettings -Vault $vault

```
```
ResourceName         ResourceGroupName ResourceNamespace          ResourceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults     
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```

För en Azure-till-Azure-migrering kan du ange valv kontexten till det nya valvet: 

```azurepowershell

#Set the vault context for the PowerShell session.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault

```

## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Förbered valvet för att starta replikering av virtuella Azure-datorer

### <a name="create-a-site-recovery-fabric-object-to-represent-the-primary-source-region"></a>Skapa ett Site Recovery Fabric-objekt för att representera primär (källa) region

Objektet Fabric i valvet representerar en Azure-region. Det primära Fabric-objektet skapas för att representera den Azure-region som de virtuella datorer som skyddas för valvet tillhör. I exemplet i den här artikeln är den virtuella datorn som skyddas i regionen USA, östra.

- Det går bara att skapa ett infrastruktur objekt per region.
- Om du tidigare har aktiverat Site Recovery replikering för en virtuell dator i Azure Portal, skapar Site Recovery ett Fabric-objekt automatiskt. Om det finns ett infrastruktur objekt för en region kan du inte skapa ett nytt.


Observera att Site Recovery åtgärder körs asynkront innan du börjar. När du startar en åtgärd skickas ett Azure Site Recovery jobb och ett jobb spårnings objekt returneras. Använd jobb spårnings objekt för att hämta den senaste statusen för jobbet (Get-ASRJob) och för att övervaka status för åtgärden.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AsrFabric -Name "A2Ademo-EastUS"
```
Om virtuella datorer från flera Azure-regioner skyddas till samma valv skapar du ett infrastruktur objekt för varje käll-Azure-region.

### <a name="create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>Skapa ett Site Recovery Fabric-objekt som representerar återställnings regionen

Objektet Recovery Fabric representerar återställnings-Azure-platsen. Virtuella datorer replikeras till och återställs till (i händelse av en redundansväxling) återställnings regionen som representeras av återställnings infrastrukturen. Den Azure-region för återställning som används i det här exemplet är västra USA 2.

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AsrFabric -Name "A2Ademo-WestUS"

```

### <a name="create-a-site-recovery-protection-container-in-the-primary-fabric"></a>Skapa en Site Recovery skydds behållare i den primära infrastruktur resursen

Skydds behållaren är en behållare som används för att gruppera replikerade objekt i en infrastruktur resurs.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-ASRProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```
### <a name="create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>Skapa en Site Recovery skydds behållare i återställnings infrastrukturen

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-ASRProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

### <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

```azurepowershell
#Create replication policy
$TempASRJob = New-ASRPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-ASRPolicy -Name "A2APolicy"
```
### <a name="create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>Skapa en skydds behållar mappning mellan den primära behållaren och återställnings skydds behållaren

En skydds container mappning mappar den primära skydds behållaren med en återställnings skydds behållare och en replikeringsprincip. Skapa en mappning för varje replikeringsprincip som du ska använda för att replikera virtuella datorer mellan ett skydds container par.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-ASRProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

### <a name="create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>Skapa en skydds behållar mappning för återställning efter fel (omvänd replikering efter en redundansväxling)

När du är redo att återställa den virtuella datorn till den ursprungliga Azure-regionen efter en redundansväxling kan du återställa efter fel. För återställning efter fel replikeras den felande över virtuella datorn från den felande över-regionen till den ursprungliga regionen. För omvänd replikering är rollerna för den ursprungliga regionen och växeln för återställnings regionen. Den ursprungliga regionen blir nu den nya återställnings regionen och det som ursprungligen var återställnings regionen blir den primära regionen. Skydds behållar mappningen för omvänd replikering representerar de växlade rollerna i original-och återställnings regionerna.

```azurepowershell
#Create Protection container mapping (for failback) between the Recovery and Primary Protection Containers with the Replication policy
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

## <a name="create-cache-storage-accounts-and-target-storage-accounts"></a>Skapa lagrings konto (n) för cache och mål lagrings konton

Ett cache Storage-konto är ett standard lagrings konto i samma Azure-region som den virtuella datorn replikeras. Lagrings kontot för cachen används för att lagra ändringar tillfälligt, innan ändringarna flyttas till Azure-regionen för återställning. Du kan välja att (men inte behöva) ange olika cache-lagrings konton för de olika diskarna på en virtuell dator.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

För virtuella datorer som **inte använder hanterade diskar**är mål lagrings kontot lagrings kontona i återställnings regionen som diskarna på den virtuella datorn replikeras till. Mål lagrings kontot kan antingen vara ett standard lagrings konto eller ett Premium Storage-konto. Välj den typ av lagrings konto som krävs baserat på data ändrings frekvensen (i/o-bildfrekvensen) för diskarna och Azure Site Recovery omsättnings gränser som stöds för lagrings typen.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage

```

## <a name="create-network-mappings"></a>Skapa nätverks mappningar

En nätverks mappning mappar virtuella nätverk i den primära regionen till virtuella nätverk i återställnings regionen. Nätverks mappningen anger det virtuella Azure-nätverket i återställnings regionen, som en virtuell dator i det primära virtuella nätverket ska redundansväxla. Ett virtuellt Azure-nätverk kan bara mappas till ett enda virtuellt Azure-nätverk i en återställnings region.

- Skapa ett virtuellt Azure-nätverk i återställnings regionen att redundansväxla till

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```
- Hämta det primära virtuella nätverket (det virtuella nätverk som den virtuella datorn är ansluten till)
   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to

    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resource name
    $NIC = Get-AzNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")  
   ```
- Skapa nätverks mappning mellan det primära virtuella nätverket och det virtuella återställnings nätverket
   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State

   ```
- Skapa nätverks mappning för omvänd riktning (failback)
    ```azurepowershell
    #Create an ASR network mapping for failback between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Replikera virtuell Azure-dator

Replikera den virtuella Azure-datorn med **Managed disks**.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data disk
$datadiskId1  = $vm.StorageProfile.DataDisks[0].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[0].StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig


#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

```

Replikera den virtuella Azure-datorn med **ohanterade diskar**.

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.  
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}


#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

När åtgärden starta replikeringen har slutförts replikeras data för virtuella datorer till återställnings regionen.

Replikeringen börjar genom att initiera en kopia av de replikerade diskarna för den virtuella datorn i återställnings regionen. Den här fasen kallas för fasen inledande replikering.

När den inledande replikeringen har slutförts flyttas replikeringen till fasen differentiell synkronisering. Den virtuella datorn är nu skyddad och en redundanstest kan utföras på den. Replikeringstillståndet för det replikerade objektet som representerar den virtuella datorn går till läget "skyddat" när den inledande replikeringen har slutförts.

Övervaka replikeringstillståndet och replikeringens hälso tillstånd för den virtuella datorn genom att hämta information om det skyddade replikerings objekt som motsvarar det.
```azurepowershell
 Get-ASRReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal           
```

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>Utföra en redundanstest, validera och rensa redundanstest

När replikeringen för den virtuella datorn har nått ett skyddat tillstånd kan en redundanstest utföras på den virtuella datorn (på det skyddade objektet i replikeringen på den virtuella datorn.)

```azurepowershell
#Create a separate network for test failover (not connected to my DR network)
$TFOVnet = New-AzVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Utför redundanstest.
```azurepowershell
$ReplicationProtectedItem = Get-ASRReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery


```

Vänta tills redundanstestning har slutförts.
```azurepowershell
Get-ASRJob -Job $TFOJob
```

```
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```
När redundanstestning har slutförts kan du ansluta till testet misslyckades över den virtuella datorn och verifiera redundanstest.

När testet har slutförts på grund av att testet misslyckades på den virtuella datorn kan du rensa test kopian genom att starta redundansväxlingen av rensnings försök. Den här åtgärden tar bort test kopian av den virtuella datorn som skapades av redundanstestningen.

```azurepowershell
$Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJob -Job $Job_TFOCleanup | Select State
```
```
State    
-----    
Succeeded
```

## <a name="failover-to-azure"></a>Redundansväxla till Azure

Redundansväxla den virtuella datorn till en viss återställnings punkt.

```azurepowershell
$RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```
```
CrashConsistent 4/24/2018 11:10:25 PM
```


```azurepowershell
#Start the failover job
$Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-ASRJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```
```
Succeeded
```

När åtgärden har misslyckats kan du genomföra redundansväxlingen.
```azurepowershell
$CommitFailoverJOb = Start-ASRCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJOb -Job $CommitFailoverJOb
```

```
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

## <a name="reprotect-and-failback-to-source-region"></a>Återaktivera skydd och återställning efter fel till käll regionen

När du är redo att gå tillbaka till den ursprungliga regionen efter en redundansväxling startar du omvänd replikering för det skyddade objektet i AzRecoveryServicesAsrProtectionDirection med cmdleten Update-.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage
```

```azurepowershell
#Use the recovery protection container, new cache storage accountin West US and the source region VM resource group
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure
-ProtectionContainerMapping $RecoveryProtContainer -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.Id
```

När skyddet har slutförts kan du initiera redundans i omvänd riktning (västra USA till USA, östra) och återställning efter fel till käll regionen.

## <a name="disable-replication"></a>Inaktivera replikering

Du kan inaktivera replikering med hjälp av cmdleten Remove-ASRReplicationProtectedItem.

```azurepowershell
Remove-ASRReplicationProtectedItem -ReplicationProtectedItem $ReplicatedItem
```

## <a name="next-steps"></a>Nästa steg
Se [Azure Site Recovery PowerShell-referensen](https://docs.microsoft.com/powershell/module/az.RecoveryServices) för att lära dig hur du kan utföra andra uppgifter, till exempel skapa återställnings planer och testa redundansväxling av återställnings planer via PowerShell.
