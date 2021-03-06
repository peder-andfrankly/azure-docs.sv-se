---
title: Konfigurera Distributionsläge för belastningsutjämnare i Azure
titleSuffix: Azure Load Balancer
description: I den här artikeln ska du komma igång med att konfigurera distributions läget för Azure Load Balancer att stödja Källans IP-tillhörighet.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: ddccd02e7157792d942309ae4f74933322f246f9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225364"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Konfigurera Distributionsläge för belastningsutjämnare i Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Hash-baserade Distributionsläge

Standard distributions läget för Azure Load Balancer är en hash med fem tupler. 

Tuppeln består av:
* **Käll-IP**
* **Källport**
* **Mål-IP**
* **Målport**
* **Protokoll typ**

Hashen används för att mappa trafik till de tillgängliga servrarna. Algoritmen ger varaktighet endast inom en transport-session. Paket i samma session dirigeras till samma data Center-IP bakom den belastningsutjämnade slut punkten. När klienten startar en ny session från samma käll-IP ändras käll porten och gör att trafiken går till en annan data Center slut punkt.

![Hash-baserat distributions läge för fem tupler](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Tillhörighetsläge för käll-IP

Belastningsutjämnaren kan också konfigureras med hjälp av distributions läget för källans IP-tillhörighet. Den här Distributionsläge kallas även sessionstillhörighet eller klient-IP-tilldelning. I läget används en hash med två tupler (käll-IP och mål-IP) eller tre tupler (käll-IP, mål-IP och protokoll typ) för att mappa trafik till de tillgängliga servrarna. Med hjälp av Källans IP-tillhörighet går anslutningar som startas från samma klient dator till samma data Center slut punkt.

Följande bild illustrerar en konfiguration med två tupler. Observera hur två tupler körs via belastningsutjämnaren till den virtuella datorn 1 (VM1). VM1 är sedan backas upp av VM2 och VM3.

![Distributions läge för två tuple-tillhörighet](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Käll-IP-tillhörighetsläge löser inkompatibilitet mellan Azure Load Balancer och fjärrskrivbordsgateway (RD Gateway). Genom att använda det här läget kan skapa du en RD Gateway-servergrupp i en enda molntjänst.

En annan användningsfall är media ladda upp. Ladda upp data sker via UDP, men kontrollplanet uppnås via TCP:

* En klient startar en TCP-session med den belastningsutjämnade offentliga adressen och dirigeras till en speciell DIP. Kanalen är fortfarande aktiv för att övervaka hälsotillståndet för anslutningen.
* En ny UDP-session från samma klient dator startas till samma belastningsutjämnade offentliga slut punkt. Anslutningen dirigeras till samma DIP-slutpunkt som den tidigare TCP-anslutningen. Media-överföringen kan utföras med högt dataflöde samtidigt som en kontrollkanal via TCP.

> [!NOTE]
> När en belastningsutjämnad uppsättning ändras genom att ta bort eller lägga till en virtuell dator, recomputed fördelningen av klientbegäranden. Du kan lita på nya anslutningar från befintliga klienter till slutar vid samma server. Dessutom använder käll-IP tillhörighetsläge distribution kan det orsaka en ojämn fördelning av trafik. Klienter som körs bakom proxyservrar kan ses som en unik klientprogram.

## <a name="configure-source-ip-affinity-settings"></a>Konfigurera inställningar för datakälla IP-tillhörighet

### <a name="azure-portal"></a>Azure Portal

Du kan ändra konfigurationen för distributions läget genom att ändra belastnings Utjämnings regeln i portalen.

1. Logga in på Azure Portal och leta upp resurs gruppen som innehåller den belastningsutjämnare som du vill ändra genom att klicka på **resurs grupper**.
2. I översikts fönstret belastningsutjämnare klickar du på **belastnings Utjämnings regler** under **Inställningar**.
3. I fönstret belastnings Utjämnings regler klickar du på den belastnings Utjämnings regel som du vill ändra distributions läget för.
4. Under regeln ändras distributions läget genom att den nedrullningsbara List rutan för **sessionens persistence** ändras.  Följande alternativ är tillgängliga:
    
    * **Ingen (Hash-baserad)** – anger att efterföljande begär Anden från samma klient kan hanteras av alla virtuella datorer.
    * **Klientens IP-adress (käll-IP-tillhörighet 2-tupel)** – anger att efterföljande förfrågningar från samma klient-IP-adress ska hanteras av samma virtuella dator.
    * **Klientens IP och protokoll (Källans IP-tillhörighet 3-tupel)** – anger att efterföljande förfrågningar från samma klient-IP-adress och protokoll kombination ska hanteras av samma virtuella dator.

5. Välj distributions läge och klicka sedan på **Spara**.

### <a name="azure-powershell"></a>Azure PowerShell

För virtuella datorer som distribueras med Resource Manager använder du PowerShell för att ändra distributions inställningarna för belastningsutjämnare för en befintlig belastnings Utjämnings regel. Följande kommando uppdaterar distributions läget: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

För klassiska virtuella datorer, använder du Azure PowerShell för att ändra inställningar för distribution. Lägg till en Azure-slutpunkt för en virtuell dator och konfigurera Distributionsläge för belastningsutjämnare:

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Ange värdet för `LoadBalancerDistribution`-elementet för mängden belastnings utjämning som krävs. Ange sourceIP för belastnings utjämning för två tupleer (käll-IP och mål-IP). Ange sourceIPProtocol för tre tupler (käll-IP, mål-IP och protokoll typ) belastnings utjämning. Ange ingen för standard beteendet för belastnings utjämning med fem tupleer.

Hämta en slutpunkt distribution läge konfigurationen för belastningsutjämnaren med hjälp av de här inställningarna:

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15
    LoadBalancerDistribution : sourceIP

Om `LoadBalancerDistribution`-elementet inte finns använder Azure Load Balancer standardalgoritmen för fem tupler.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Konfigurera Distributionsläge på belastningsutjämnad slutpunktsuppsättning

När slutpunkterna är en del av en belastningsutjämnad slutpunktsuppsättning, måste Distributionsläge konfigureras på belastningsutjämnad slutpunktsuppsättning:

```azurepowershell-interactive
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Konfigurera Distributionsläge för Cloud Services-slutpunkter

Använd Azure SDK för .NET 2.5 för att uppdatera din molntjänst. Slutpunktsinställningarna för molntjänster görs i .csdef-filen. Om du vill uppdatera Distributionsläge för belastningsutjämnare för en Cloud Services-distribution, krävs en distributionsuppgradering av.

Här är ett exempel på .csdef ändringar av inställningarna för slutpunkten:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
<InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
    <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
</InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="api-example"></a>API-exempel

I följande exempel visas hur du konfigurerar om Distributionsläge för belastningsutjämnare för en angiven mängd med Utjämning av nätverksbelastning i en distribution. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Ändra Distributionsläge för distribuerade belastningsutjämnad uppsättning

Använd den klassiska distributionsmodellen för att ändra en befintlig distributionskonfiguration. Lägg till `x-ms-version` rubriken och ange värdet till version 2014-09-01 eller senare.

#### <a name="request"></a>Förfrågan

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

Som tidigare har beskrivits, ställer du in `LoadBalancerDistribution`-elementet på sourceIP för två tuple-tillhörighet, sourceIPProtocol för par med tre tupler eller ingen för ingen tillhörighet (5 tuple tillhörighet).

#### <a name="response"></a>Svar

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Nästa steg

* [Översikt över interna Azure-Load Balancer](load-balancer-internal-overview.md)
* [Kom igång med att konfigurera en Internet-riktad belastningsutjämnare](load-balancer-get-started-internet-arm-ps.md)
* [Konfigurera timeout-inställningar för inaktiv TCP för en lastbalanserare](load-balancer-tcp-idle-timeout.md)
