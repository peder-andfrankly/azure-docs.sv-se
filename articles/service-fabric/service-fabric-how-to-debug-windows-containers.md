---
title: Felsöka Windows-behållare med Service Fabric och VS | Microsoft Docs
description: Lär dig att felsöka Windows-behållare i Azure Service Fabric med hjälp av Visual Studio 2019.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: mikhegn
ms.openlocfilehash: a5ccf527850e1c05c5d7e273ada905d65d64cee4
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073963"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>Anvisningar: Felsöka Windows-behållare i Azure Service Fabric med Visual Studio 2019

Med Visual Studio 2019 kan du felsöka .NET-program i behållare som Service Fabric Services. Den här artikeln visar hur du konfigurerar din miljö och sedan felsöka ett .NET-program i en behållare som körs i ett lokalt Service Fabric-kluster.

## <a name="prerequisites"></a>Förutsättningar

* I Windows 10 följer du den här snabb starten för att [Konfigurera Windows 10 att köra Windows-behållare](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* På Windows Server 2016 följer du den här snabb starten för att [Konfigurera windows 2016 för att köra Windows-behållare](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* Konfigurera din lokala Service Fabrics miljö genom att följa [förbereda utvecklings miljön i Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)

## <a name="configure-your-developer-environment-to-debug-containers"></a>Konfigurera din utvecklings miljö för att felsöka behållare

1. Kontrol lera att Docker för Window-tjänsten körs innan du fortsätter med nästa steg.

1. För att stödja DNS-matchning mellan behållare måste du konfigurera ditt lokala utvecklings kluster med hjälp av dator namnet. De här stegen är också nödvändiga om du vill adressera tjänster via den omvända proxyn.
   1. Öppna PowerShell som administratör
   2. Gå till mappen för konfiguration av SDK-kluster `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`, vanligt vis.
   3. Kör skriptet`DevClusterSetup.ps1`

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > Du kan använda `-CreateOneNodeCluster` för att konfigurera ett kluster med en nod. Som standard skapas ett lokalt kluster med fem noder.
      >

      Mer information om DNS-tjänsten i Service Fabric finns i [DNS-tjänsten i Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice). Om du vill veta mer om hur du använder Service Fabric omvänd proxy från tjänster som körs i en behållare, se [omvänd proxy särskild hantering för tjänster som körs i behållare](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Kända begränsningar vid fel sökning av behållare i Service Fabric

Nedan visas en lista över kända begränsningar med fel sökning av behållare i Service Fabric och möjliga lösningar:

* Användning av localhost för ClusterFQDNorIP stöder inte DNS-matchning i behållare.
    * Lösning: Konfigurera det lokala klustret med hjälp av dator namnet (se ovan)
* Att köra windows10 på en virtuell dator får inte DNS-svar tillbaka till behållaren.
    * Lösning: Inaktivera avlastning av UDP-kontrollsumma för IPv4 på Virtual Machines NÄTVERKSKORTet
    * Om du kör windows10 försämras nätverkets prestanda på datorn.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* Att lösa tjänster i samma program som använder DNS-tjänstens namn fungerar inte på windows10, om programmet har distribuerats med Docker Compose
    * Lösning: Använd ServiceName. ApplicationName för att lösa tjänst slut punkter
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Om du använder IP-adress för ClusterFQDNorIP, kommer att ändra primär IP på värden att avbryta DNS-funktionerna.
    * Lösning: Återskapa klustret med den nya primära IP-adressen på värden eller Använd dator namn. Detta brott är avsiktligt.
* Om det FQDN-namn som klustret skapades med inte kan matchas i nätverket kommer DNS att Miss förväntas.
    * Lösning: Återskapa det lokala klustret med hjälp av värdens primära IP-adress. Det här felet är avsiktligt.
* Vid fel sökning av en behållare kommer Docker-loggar bara att vara tillgängliga i Visual Studio-utdatafönstret, inte via Service Fabric API: er, inklusive Service Fabric Explorer

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Felsöka ett .NET-program som körs i Docker-behållare på Service Fabric

1. Kör Visual Studio som administratör.

1. Öppna ett befintligt .NET-program eller skapa ett nytt.

1. Högerklicka på projektet och välj **Add-> container Orchestrator Support-> Service Fabric**

1. Tryck på **F5** för att starta fel sökningen av programmet.

    Visual Studio stöder konsol-och ASP.NET projekt typer för .NET och .NET Core.

## <a name="next-steps"></a>Nästa steg
Mer information om funktionerna i Service Fabric och behållare finns i [Översikt över Service Fabric behållare](service-fabric-containers-overview.md).
