---
title: Spara filer i Azure Cloud Shell | Microsoft Docs
description: Genom gång av hur Azure Cloud Shell sparar filer.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/20/2019
ms.author: damaerte
ms.openlocfilehash: 8e04e7c1919deaf60e083aba4588943147ebd6bf
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284814"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Bevara filer i Azure Cloud Shell
Cloud Shell använder Azure File Storage för att spara filer mellan sessioner. Vid inledande start blir Cloud Shell att du kan associera en ny eller befintlig fil resurs för att spara filer mellan sessioner.

> [!NOTE]
> Bash och PowerShell delar samma fil resurs. Det går bara att koppla en fil resurs till automatisk montering i Cloud Shell.

## <a name="create-new-storage"></a>Skapa ny lagring

När du använder grundläggande inställningar och bara väljer en prenumeration skapar Cloud Shell tre resurser för din räkning i den region som stöds, som är närmast dig:
* Resursgrupp: `cloud-shell-storage-<region>`
* Lagrings konto: `cs<uniqueGuid>`
* Fil resurs: `cs-<user>-<domain>-com-<uniqueGuid>`

![Prenumerations inställningen](media/persisting-shell-storage/basic-storage.png)

Fil resursen monteras som `clouddrive` i `$Home`s katalogen. Detta är en engångs åtgärd och fil resursen monteras automatiskt i efterföljande sessioner. 

Fil resursen innehåller också en avbildning på 5 GB som skapas för dig och som automatiskt sparar data i `$Home`s katalogen. Detta gäller både bash och PowerShell.

## <a name="use-existing-resources"></a>Använd befintliga resurser

Med alternativet Avancerat kan du associera befintliga resurser. När du väljer en Cloud Shell region måste du välja ett lagrings konto som finns i samma region. Om din tilldelade region t. ex. är västra USA måste du associera en fil resurs som finns i västra USA.

När installations programmet för lagring visas väljer du **Visa avancerade inställningar** för att visa fler alternativ. De ifyllda lagrings alternativ filtren för lokalt redundant lagring (LRS), Geo-redundant lagring (GRS) och ZRS-konton (Zone-redundant lagring). 

> [!NOTE]
> Du rekommenderas att använda GRS-eller ZRS-lagrings konton för ytterligare återhämtning för din fil resurs. Vilken typ av redundans beror på dina mål och pris inställningar. [Läs mer om replikeringsalternativ för Azure Storage-konton](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

![Resurs grupps inställningen](media/persisting-shell-storage/advanced-storage.png)

## <a name="securing-storage-access"></a>Skydda lagrings åtkomst
För säkerhet bör varje användare etablera sitt eget lagrings konto.  För rollbaserad åtkomst kontroll (RBAC) måste användare ha deltagar åtkomst eller högre på lagrings konto nivå.

Cloud Shell använder en Azure-filresurs i ett lagrings konto i en angiven prenumeration. På grund av ärvda behörigheter kommer användare med tillräcklig åtkomst behörighet till prenumerationen att kunna komma åt alla lagrings konton och fil resurser som ingår i prenumerationen.

Användarna bör låsa åtkomsten till sina filer genom att ange behörigheterna på lagrings kontot eller på prenumerations nivån.

## <a name="supported-storage-regions"></a>Lagrings regioner som stöds
Associerade Azure Storage-konton måste finnas i samma region som den Cloud Shell datorn som du monterar dem på. Om du vill hitta din aktuella region kan du köra `env` i bash och leta upp variabeln `ACC_LOCATION`. Fil resurser får en avbildning på 5 GB som skapats så att du kan spara din `$Home` katalog.

Det finns Cloud Shell datorer i följande regioner:

|Område|Region|
|---|---|
|Nord- och Sydamerika|Östra USA, södra centrala USA, västra USA|
|Europa|Norra Europa, västra Europa|
|Asien och stillahavsområdet|Indien, centrala, Sydostasien|

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Begränsa resurs skapande med en Azure-resurs princip
De lagrings konton som du skapar i Cloud Shell taggas med `ms-resource-usage:azure-cloud-shell`. Om du inte vill att användare ska kunna skapa lagrings konton i Cloud Shell skapar du en [princip för Azure-resurser för Taggar](../azure-policy/json-samples.md) som utlösts av den här taggen.

## <a name="how-cloud-shell-storage-works"></a>Så här fungerar Cloud Shell Storage 
Cloud Shell sparar filer genom båda följande metoder: 
* Skapa en disk avbildning av din `$Home` katalog för att spara allt innehåll i katalogen. Disk avbildningen sparas i den angivna fil resursen som `acc_<User>.img` på `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`och synkroniserar automatiskt ändringar. 
* Montera den angivna fil resursen som `clouddrive` i din `$Home` Directory för direkt interaktion av fil resurser. `/Home/<User>/clouddrive` mappas till `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Alla filer i `$Home`-katalogen, till exempel SSH-nycklar, finns kvar i din användar disk avbildning, som lagras i den monterade fil resursen. Använd metod tips när du bevarar information i `$Home`s katalogen och den monterade fil resursen.

## <a name="clouddrive-commands"></a>clouddrive-kommandon

### <a name="use-the-clouddrive-command"></a>Använda kommandot `clouddrive`
I Cloud Shell kan du köra ett kommando som heter `clouddrive`, vilket gör att du kan uppdatera fil resursen som är monterad till Cloud Shell manuellt.
![att köra kommandot "clouddrive"](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>Lista `clouddrive`
Om du vill identifiera vilken fil resurs som är monterad som `clouddrive`kör du kommandot `df`. 

Fil Sök vägen till clouddrive visar ditt lagrings konto namn och fil resurs i URL: en. Till exempel, `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

### <a name="mount-a-new-clouddrive"></a>Montera en ny clouddrive

#### <a name="prerequisites-for-manual-mounting"></a>Krav för manuell montering
Du kan uppdatera fil resursen som är kopplad till Cloud Shell med hjälp av `clouddrive mount` kommandot.

Om du monterar en befintlig fil resurs måste lagrings kontona finnas i din SELECT Cloud Shell-region. Hämta platsen genom att köra `env` och kontrol lera `ACC_LOCATION`.

#### <a name="the-clouddrive-mount-command"></a>Kommandot `clouddrive mount`

> [!NOTE]
> Om du monterar en ny fil resurs skapas en ny användar avbildning för din `$Home` katalog. Din tidigare `$Home`-avbildning sparas i din tidigare fil resurs.

Kör kommandot `clouddrive mount` med följande parametrar:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Om du vill visa mer information kör `clouddrive mount -h`, som du ser här:

![Kör clouddrive-mount'command](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Demontera clouddrive
Du kan demontera en fil resurs som är monterad till Cloud Shell när som helst. Eftersom Cloud Shell kräver att en monterad fil resurs används uppmanas du att skapa och montera en annan fil resurs i nästa session.

1. Kör `clouddrive unmount`.
2. Bekräfta och bekräfta prompter.

Fil resursen kommer att finnas kvar om du inte tar bort den manuellt. Cloud Shell kommer inte längre att söka efter den här fil resursen på efterföljande sessioner. Om du vill visa mer information kör `clouddrive unmount -h`, som du ser här:

![Kör clouddrive-unmount'command](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Även om du kör det här kommandot tar du inte bort några resurser, manuellt tar bort en resurs grupp, ett lagrings konto eller en fil resurs som är mappad till Cloud Shell raderar `$Home` katalog disk avbildningen och eventuella filer i fil resursen. Det går inte att utföra den här åtgärden.
## <a name="powershell-specific-commands"></a>PowerShell-/regionsspecifika kommandon

### <a name="list-clouddrive-azure-file-shares"></a>Lista `clouddrive` Azure-filresurser
Den `Get-CloudDrive` cmdlet: en hämtar information om Azure-filresurser som för närvarande monteras av `clouddrive` i Cloud Shell. <br>
![som kör get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Demontera `clouddrive`
Du kan demontera en Azure-filresurs som är monterad till Cloud Shell när som helst. Om Azure-filresursen har tagits bort uppmanas du att skapa och montera en ny Azure-fil resurs vid nästa session.

`Dismount-CloudDrive`-cmdleten demonterar en Azure-filresurs från det aktuella lagrings kontot. Om du demonterar `clouddrive` avbryts den aktuella sessionen. Användaren uppmanas att skapa och montera en ny Azure-fil resurs under nästa session.
![som kör dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Obs: om du behöver definiera en funktion i en fil och anropa den från PowerShell-cmdletarna måste punkt operatorn inkluderas. Till exempel:. .\MyFunctions.ps1

## <a name="next-steps"></a>Nästa steg
[Cloud Shell snabb start](quickstart.md) <br>
[Läs mer om lagring av Microsoft Azure filer](https://docs.microsoft.com/azure/storage/storage-introduction) <br>
[Läs mer om lagrings märken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
