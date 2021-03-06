---
title: Konfigurera en haveri beredskap för Hyper-V (med VMM) till en sekundär plats med Azure Site Recovery/PowerShell
description: Beskriver hur du konfigurerar haveri beredskap för virtuella Hyper-V-datorer i VMM-moln till en sekundär VMM-plats med hjälp av Azure Site Recovery och PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 2fc66514bdf33611f9e6266d35a2d537fe3b9261
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084909"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Konfigurera katastrof återställning av virtuella Hyper-V-datorer till en sekundär plats med hjälp av PowerShell (Resource Manager)

Den här artikeln visar hur du automatiserar stegen för replikering av virtuella Hyper-V-datorer i System Center Virtual Machine Manager moln till ett Virtual Machine Manager moln på en sekundär lokal plats genom att använda [Azure Site Recovery](site-recovery-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

- Granska [arkitekturen och komponenterna för scenariot](hyper-v-vmm-architecture.md).
- Granska [kraven för stöd](site-recovery-support-matrix-to-sec-site.md) för alla komponenter.
- Kontrol lera att Virtual Machine Manager-servrar och Hyper-V-värdar följer [support kraven](site-recovery-support-matrix-to-sec-site.md).
- Kontrol lera att de virtuella datorer som du vill replikera följer den [replikerade datorns support](site-recovery-support-matrix-to-sec-site.md).


## <a name="prepare-for-network-mapping"></a>Förbered för nätverksmappning

[Nätverks mappningen](hyper-v-vmm-network-mapping.md) mappar mellan lokala Virtual Machine Manager virtuella dator nätverk i käll-och mål moln. Mappning utför följande:

- Ansluter virtuella datorer till lämpliga VM-målnätverk efter redundansväxling. 
- Placerar virtuella replikdatorer optimalt på Hyper-V-målvärdservrar. 
- Om du inte konfigurerar nätverks mappning ansluts virtuella replik datorer till ett virtuellt dator nätverk efter redundansväxlingen.

Förbered Virtual Machine Manager enligt följande:

* Kontrol lera att du har [Virtual Machine Manager logiska nätverk](https://docs.microsoft.com/system-center/vmm/network-logical) på käll-och mål Virtual Machine Manager servrarna:

    - Det logiska nätverket på källservern ska vara associerat med det källmoln där Hyper-V-värdarna finns.
    - Det logiska nätverket på målservern ska vara associerat med målmolnet.
* Kontrol lera att du har [VM-nätverk](https://docs.microsoft.com/system-center/vmm/network-virtual) på käll-och mål Virtual Machine Managers servrarna. VM-nätverk ska vara länkade till det logiska nätverket på varje plats.
* Ansluta virtuella datorer på Hyper-V-källvärdar till VM-källnätverket. 

## <a name="prepare-for-powershell"></a>Förbered för PowerShell

Kontrol lera att du har Azure PowerShell redo att gå:

- Om du redan använder PowerShell uppgraderar du till version 0.8.10 eller senare. [Läs mer](/powershell/azureps-cmdlets-docs) om hur du konfigurerar PowerShell.
- När du har konfigurerat och konfigurerat PowerShell granskar du [tjänst-cmdletarna](/powershell/azure/overview).
- Om du vill veta mer om hur du använder parameter värden, indata och utdata i PowerShell läser du guiden [Kom igång](/powershell/azure/get-started-azureps) .

## <a name="set-up-a-subscription"></a>Konfigurera en prenumeration
1. Från PowerShell loggar du in på ditt Azure-konto.

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Connect-AzAccount #-Credential $Cred
2. Hämta en lista med dina prenumerationer med prenumerations-ID: n. Notera ID: t för den prenumeration som du vill skapa Recovery Services valvet i. 

        Get-AzSubscription
3. Ange prenumerationen för valvet.

        Set-AzContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv
1. Skapa en Azure Resource Manager resurs grupp om du inte har någon.

        New-AzResourceGroup -Name #ResourceGroupName -Location #location
2. Skapa ett nytt Recovery Services-valv. Spara valvet-objektet i en variabel som ska användas senare. 

        $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   
    Du kan hämta valvet-objektet när du har skapat det med hjälp av cmdleten Get-AzRecoveryServicesVault.

## <a name="set-the-vault-context"></a>Ange valv kontext
1. Hämta ett befintligt valv.

       $vault = Get-AzRecoveryServicesVault -Name #vaultname
2. Ange valv kontexten.

       Set-AzSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-site-recovery-provider"></a>Installera Site Recovery-providern
1. Skapa en katalog på Virtual Machine Manager datorn genom att köra följande kommando:

       New-Item c:\ASR -type directory
2. Extrahera filerna med hjälp av installations filen för den hämtade providern.

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Installera providern och vänta tills installationen är klar.

       .\SetupDr.exe /i
       $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
       do
       {
         $isNotInstalled = $true;
         if(Test-Path $installationRegPath)
         {
           $isNotInstalled = $false;
         }
       }While($isNotInstalled)

4. Registrera servern i valvet.

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="create-and-associate-a-replication-policy"></a>Skapa och koppla en replikeringsprincip
1. Skapa en replikeringsprincip, i det här fallet för Hyper-V 2012 R2, enligt följande:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > Virtual Machine Manager molnet kan innehålla Hyper-V-värdar som kör olika versioner av Windows Server, men replikeringsprincipen är en särskild version av ett operativ system. Om du har olika värdar som kör på olika operativ system kan du skapa separata replikeringsprinciper för varje system. Om du till exempel har fem värdar som kör på Windows Server 2012 och tre värdar som kör på Windows Server 2012 R2, skapar du två replikeringsprinciper. Du skapar ett för varje typ av operativ system.

2. Hämta den primära skydds containern (primär Virtual Machine Manager moln) och återställnings skydds behållare (återställnings Virtual Machine Manager moln).

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
3. Hämta den replikeringsprincip du skapade med hjälp av det egna namnet.

       $policy = Get-AzSiteRecoveryPolicy -FriendlyName $policyname
4. Starta associationen av skydds containern (Virtual Machine Manager molnet) med replikeringsprincipen.

       $associationJob  = Start-AzSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
5. Vänta tills princip associerings jobbet har slutförts. Om du vill kontrol lera om jobbet är klart använder du följande PowerShell-kodfragment:

       $job = Get-AzSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

6. När jobbet har slutfört bearbetningen kör du följande kommando:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Följ stegen i [Övervaka aktivitet](#monitor-activity)för att kontrol lera att åtgärden har slutförts.

##  <a name="configure-network-mapping"></a>Konfigurera nätverksmappning
1. Använd det här kommandot för att hämta servrar för det aktuella valvet. Kommandot lagrar Site Recovery-servrar i $Servers mat ris variabeln.

        $Servers = Get-AzSiteRecoveryServer
2. Kör det här kommandot för att hämta nätverken för käll Virtual Machine Managers servern och mål Virtual Machine Manager servern.

        $PrimaryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > Käll Virtual Machine Managers servern kan vara den första eller andra i Server matrisen. Kontrol lera Virtual Machine Manager Server namn och hämta nätverken på rätt sätt.


3. Denna cmdlet skapar en mappning mellan det primära nätverket och återställnings nätverket. Den anger det primära nätverket som det första elementet i $PrimaryNetworks. Den anger återställnings nätverket som det första elementet i $RecoveryNetworks.

        New-AzSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>Aktivera skydd för virtuella datorer
När servrarna, molnen och nätverken har kon figurer ATS korrekt aktiverar du skyddet för virtuella datorer i molnet.

1. Kör följande kommando för att hämta skydds behållaren för att aktivera skydd:

          $PrimaryProtectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Hämta skydds enheten (VM) enligt följande:

           $protectionEntity = Get-AzSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Aktivera replikering för den virtuella datorn.

          $jobResult = Set-AzSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>Köra ett redundanstest

Testa distributionen genom att köra ett redundanstest för en enskild virtuell dator. Du kan också skapa en återställnings plan som innehåller flera virtuella datorer och köra ett redundanstest för planen. Ett redundanstest simulerar redundans- och återställningsmekanismen i ett isolerat nätverk.

1. Hämta den virtuella datorn dit de virtuella datorerna ska redundansväxla.

       $Servers = Get-AzSiteRecoveryServer
       $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

2. Utför ett redundanstest.

   För en enskild virtuell dator:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
   För en återställnings plan:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

Följ stegen i [Övervaka aktivitet](#monitor-activity)för att kontrol lera att åtgärden har slutförts.

## <a name="run-planned-and-unplanned-failovers"></a>Köra planerade och oplanerad redundans

1. Utföra en planerad redundansväxling.

   För en enskild virtuell dator:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   För en återställnings plan:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

2. Utföra en oplanerad redundansväxling.

   För en enskild virtuell dator:
        
        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   För en återställnings plan:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>Övervaka aktivitet
Använd följande kommandon för att övervaka redundansväxling. Vänta tills bearbetningen har slutförts mellan jobb.

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

    if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>Nästa steg

[Läs mer](/powershell/module/az.recoveryservices) om hur du Site Recovery med PowerShell-cmdletar för Resource Manager.
