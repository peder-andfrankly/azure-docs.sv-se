---
title: Azure Service Fabric Node-typer och skalnings uppsättningar för virtuella datorer | Microsoft Docs
description: Lär dig hur Azure Service Fabric Node-typer relaterar till skalnings uppsättningar för virtuella datorer och hur du fjärransluter till en instans av en skalnings uppsättning eller klusternod.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: pepogors
ms.openlocfilehash: cec134f9e71f86cd0ed17912f1a3c76adc9a4164
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167325"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Azure Service Fabric Node-typer och skalnings uppsättningar för virtuella datorer
[Skalnings uppsättningar för virtuella datorer](/azure/virtual-machine-scale-sets) är en Azure Compute-resurs. Du kan använda skalnings uppsättningar för att distribuera och hantera en samling virtuella datorer som en uppsättning. Varje nodtyp som du definierar i ett Azure Service Fabric-kluster ställer in en separat skala.  Service Fabric Runtime installerat på varje virtuell dator i skalnings uppsättningen av tillägget Microsoft. Azure. ServiceFabric för virtuella datorer. Du kan skala upp eller ned varje nodtyp separat, ändra OS-SKU: n som körs på varje klusternod, ha olika portar öppna och använda olika kapacitets mått.

Följande bild visar ett kluster som har två nodtyper, med namnet FrontEnd och Server del. Varje nodtyp har fem noder.

![Ett kluster som har två nodtyper][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mappa virtuella datorers skalnings uppsättnings instanser till noder
Som du ser i föregående bild, startar skalnings uppsättnings instanserna vid instans 0 och ökar sedan med 1. Numreringen visas i nodnamn. Node BackEnd_0 är till exempel instans 0 av Server dels skalnings uppsättningen. Denna specifika skalnings uppsättning har fem instanser, med namnet BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 och BackEnd_4.

När du skalar upp en skalnings uppsättning skapas en ny instans. Det nya instans namnet för skalnings uppsättningen är vanligt vis skalnings uppsättningens namn plus nästa instans nummer. I vårt exempel är det BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Mappa skal uppsättnings belastningsutjämnare till nodtyper och skalnings uppsättningar
Om du har distribuerat klustret i Azure Portal eller använt exemplet Azure Resource Manager mall visas alla resurser under en resurs grupp. Du kan se belastnings utjämning för varje skalnings uppsättning eller nodtyp. Belastnings Utjämnings namnet har följande format: **lb-&lt;nodnamn&gt;** . Ett exempel är LB-sfcluster4doc-0, som du ser i följande figur:

![Resurser][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Service Fabric tillägg för virtuell dator
Service Fabric tillägg för virtuell dator används för att starta Service Fabric till Azure Virtual Machines och konfigurera nodens säkerhet.

Följande är ett fragment med Service Fabric virtuell dators tillägg:

```json
"extensions": [
  {
    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
    "properties": {
      "type": "ServiceFabricLinuxNode",
      "autoUpgradeMinorVersion": true,
      "protectedSettings": {
        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
       },
       "publisher": "Microsoft.Azure.ServiceFabric",
       "settings": {
         "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
         "nodeTypeRef": "[variables('vmNodeType0Name')]",
         "durabilityLevel": "Silver",
         "enableParallelJobs": true,
         "nicPrefixOverride": "[variables('subnet0Prefix')]",
         "dataPath": "D:\\\\SvcFab",
         "certificate": {
           "commonNames": [
             "[parameters('certificateCommonName')]"
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
         }
       },
       "typeHandlerVersion": "1.1"
     }
   },
```

Följande är egenskaps beskrivningarna:

| **Namn** | **Tillåtna värden** | ** --- ** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
| namn | sträng | --- | unikt namn för tillägg |
| typ | "ServiceFabricLinuxNode" eller "ServiceFabricWindowsNode" | --- | Identifierar OS-Service Fabric som startas |
| autoUpgradeMinorVersion | Sant eller falskt | --- | Aktivera automatisk uppgradering av SF runtime minor-versioner |
| publisher | Microsoft.Azure.ServiceFabric | --- | namnet på Service Fabric omfattnings utgivaren |
| clusterEndpont | sträng | --- | URI: PORT till hanterings slut punkt |
| nodeTypeRef | sträng | --- | namn på nodeType |
| durabilityLevel | bronze, silver, gold, platinum | --- | tid som tillåts för att pausa oåterkallelig Azure-infrastruktur |
| enableParallelJobs | Sant eller falskt | --- | Aktivera Compute ParallelJobs som ta bort virtuell dator och starta om den virtuella datorn i samma skalnings uppsättning parallellt |
| nicPrefixOverride | sträng | --- | Undernätsprefixet som "10.0.0.0/24" |
| commonNames | sträng [] | --- | Vanliga namn på installerade kluster certifikat |
| x509StoreName | sträng | --- | Namn på arkivet där det installerade kluster certifikatet finns |
| typeHandlerVersion | 1.1 | --- | Version av tillägget. 1,0 Classic-versionen av tillägget rekommenderas för att uppgradera till 1,1 |
| dataPath | sträng | --- | Sökväg till enheten som används för att spara tillstånd för Service Fabric system tjänster och program data. 

## <a name="next-steps"></a>Nästa steg
* Se [översikten över funktionen "distribuera överallt" och en jämförelse med Azure-hanterade kluster](service-fabric-deploy-anywhere.md).
* Lär dig mer om [kluster säkerhet](service-fabric-cluster-security.md).
* [Fjärrans luta](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) till en bestämd skalnings uppsättnings instans
* [Uppdatera RDP-portens intervall värden](./scripts/service-fabric-powershell-change-rdp-port-range.md) på virtuella kluster datorer efter distributionen
* [Ändra administratörens användar namn och lösen ord](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) för virtuella kluster datorer

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
