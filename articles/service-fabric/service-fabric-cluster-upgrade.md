---
title: Uppgradera ett Azure Service Fabric-kluster
description: Lär dig mer om att uppgradera versionen eller konfigurationen av ett Azure Service Fabric-kluster.  I den här artikeln beskrivs inställning av kluster uppdaterings läge, uppgradering av certifikat, tillägg av program portar, operativ Systems uppdateringar och vad du kan förvänta när uppgraderingen utförs
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: atsenthi
ms.openlocfilehash: 161c720fbcc9370aaf273b241e88a7184f47371b
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013310"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>Uppgradera och uppdatera ett Azure Service Fabric-kluster

För alla moderna system är det viktigt att utforma för att kunna uppnå långsiktig framgång för din produkt. Ett Azure Service Fabric-kluster är en resurs som du äger, men som hanteras delvis av Microsoft. I den här artikeln beskrivs vad som hanteras automatiskt och vad du kan konfigurera själv.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Styra den infrastruktur resurs version som körs i klustret

Se till att låta klustret köra en [infrastruktur version som stöds](service-fabric-versions.md) alltid. När vi meddelar lanseringen av en ny version av Service Fabric, markeras den tidigare versionen för slut för ande av support efter minst 60 dagar från det datumet. De nya versionerna visas i Service Fabric-teamets blogg. Den nya versionen är tillgänglig och kan väljas.

14 dagar före utgången av den version som klustret körs på, genereras en hälso händelse som placerar klustret i ett varnings hälso tillstånd. Klustret är i ett varnings tillstånd tills du uppgraderar till en infrastruktur version som stöds.

Du kan ange att klustret ska ta emot automatiska Fabric-uppgraderingar när de släpps av Microsoft, eller så kan du välja en infrastruktur version som stöds som du vill att klustret ska vara på.  Läs mer i [Service Fabric versionen av klustret](service-fabric-cluster-upgrade-version-azure.md)om du vill veta mer.

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>Beteende vid uppgradering av infrastruktur under automatiska uppgraderingar
Microsoft underhåller infrastruktur koden och konfigurationen som körs i ett Azure-kluster. Vi utför automatiskt övervakade uppgraderingar till program varan vid behov. Dessa uppgraderingar kan vara kod, konfiguration eller både och. För att se till att ditt program påverkar ingen påverkan eller minimal påverkan på grund av dessa uppgraderingar, utför vi uppgraderingar i följande faser:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fas 1: en uppgradering utförs med alla kluster hälso principer
Under den här fasen fortsätter uppgraderingen en uppgraderings domän i taget och de program som kördes i klustret fortsätter att köras utan avbrott. Kluster hälso principerna (en kombination av nods hälsa och hälso tillståndet alla program som körs i klustret) följer under uppgraderingen.

Om klustrets hälso principer inte uppfylls, återställs uppgraderingen. Sedan skickas ett e-postmeddelande till Prenumerationens ägare. E-postmeddelandet innehåller följande information:

* Ett meddelande om att en kluster uppgradering skulle återställas.
* Föreslagna åtgärds åtgärder, om sådana finns.
* Antalet dagar (n) tills vi kör fas 2.

Vi försöker utföra samma uppgradering några gånger om eventuella uppgraderingar inte kunde utföras av infrastruktur skäl. Efter de n dagar från det datum då e-postmeddelandet skickades, fortsätter vi till fas 2.

Om klustrets hälso principer är uppfyllda betraktas uppgraderingen som lyckad och har marker ATS som slutförd. Detta kan inträffa under den inledande uppgraderingen eller någon av uppgraderings omgången i den här fasen. Det finns ingen e-postbekräftelse för lyckad körning. Det här är för att undvika att skicka för många e-postmeddelanden – att ta emot ett e-postmeddelande bör visas som ett undantag till normal. Vi förväntar oss att de flesta av kluster uppgraderingarna lyckas utan att påverka din program tillgänglighet.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fas 2: en uppgradering utförs endast med standard hälso principer
Hälso principerna i den här fasen anges på ett sådant sätt att antalet program som är felfria i början av uppgraderingen förblir samma under uppgraderings processens varaktighet. Som i fas 1 fortsätter fas 2-uppgraderingar en uppgraderings domän i taget och de program som kördes i klustret fortsätter att köras utan avbrott. Kluster hälso principerna (en kombination av nods hälsa och hälso tillståndet alla program som körs i klustret) följer under uppgraderingen.

Om klustrets hälso principer i själva verket inte uppfylls, återställs uppgraderingen. Sedan skickas ett e-postmeddelande till Prenumerationens ägare. E-postmeddelandet innehåller följande information:

* Ett meddelande om att en kluster uppgradering skulle återställas.
* Föreslagna åtgärds åtgärder, om sådana finns.
* Antalet dagar (n) tills vi kör fas 3.

Vi försöker utföra samma uppgradering några gånger om eventuella uppgraderingar inte kunde utföras av infrastruktur skäl. En påminnelse via e-post skickas ett par dagar innan n dagar är upp. Efter de n dagar från det datum då e-postmeddelandet skickades, fortsätter vi till fas 3. E-postmeddelandena som vi skickar till dig i fas 2 måste fattas allvarligt och åtgärder måste vidtas.

Om klustrets hälso principer är uppfyllda betraktas uppgraderingen som lyckad och har marker ATS som slutförd. Detta kan inträffa under den inledande uppgraderingen eller någon av uppgraderings omgången i den här fasen. Det finns ingen e-postbekräftelse för lyckad körning.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fas 3: en uppgradering utförs med hjälp av aggressiva hälso principer
De här hälso principerna i den här fasen är inriktad på att uppgraderingen slutförs i stället för programmens hälsa. Några kluster uppgraderingar slutförs i den här fasen. Om klustret kommer till den här fasen, är det en chans att programmet blir ohälsosamt och/eller förlorar tillgänglighet.

På samma sätt som i de andra två faserna uppgraderar fas 3 en uppgraderings domän i taget.

Om klustrets hälso principer inte uppfylls, återställs uppgraderingen. Vi försöker utföra samma uppgradering några gånger om eventuella uppgraderingar inte kunde utföras av infrastruktur skäl. Därefter fästs klustret så att det inte längre får support och/eller uppgraderingar.

Ett e-postmeddelande med den här informationen skickas till Prenumerationens ägare, tillsammans med åtgärder för att åtgärda problemet. Vi förväntar sig inte några kluster att komma in i ett tillstånd där fas 3 har misslyckats.

Om klustrets hälso principer är uppfyllda betraktas uppgraderingen som lyckad och har marker ATS som slutförd. Detta kan inträffa under den inledande uppgraderingen eller någon av uppgraderings omgången i den här fasen. Det finns ingen e-postbekräftelse för lyckad körning.

## <a name="manage-certificates"></a>Hantera certifikat
Service Fabric använder de [X. 509-Server certifikat](service-fabric-cluster-security.md) som du anger när du skapar ett kluster för att skydda kommunikationen mellan klusternoder och autentisera klienter. Du kan lägga till, uppdatera eller ta bort certifikat för klustret och klienten i [Azure Portal](https://portal.azure.com) eller med PowerShell/Azure CLI.  Läs mer i [Lägg till eller ta bort certifikat](service-fabric-cluster-security-update-certs-azure.md)

## <a name="open-application-ports"></a>Öppna program portar
Du kan ändra program portar genom att ändra Load Balancer resurs egenskaper som är associerade med nodtypen. Du kan använda Azure Portal, eller så kan du använda PowerShell/Azure CLI. Mer information finns i [öppna program portar för ett kluster](create-load-balancer-rule.md).

## <a name="define-node-properties"></a>Definiera egenskaper för nod
Ibland kanske du vill se till att vissa arbets belastningar bara körs på vissa typer av noder i klustret. En viss arbets belastning kan till exempel kräva GPU: er eller SSD medan andra inte är det. För varje nodtyp i ett kluster kan du lägga till anpassade Node-egenskaper till klusternoder. Placerings begränsningar är de instruktioner som är kopplade till enskilda tjänster som väljs för en eller flera Node-egenskaper. Placerings begränsningar definierar var tjänsterna ska köras.

Mer information om hur du använder placerings begränsningar, Node-egenskaper och hur du definierar dem finns i [Egenskaper för noden och placerings begränsningar](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="add-capacity-metrics"></a>Lägg till kapacitets mått
För var och en av nodtypen kan du lägga till anpassade kapacitets mått som du vill använda i dina program för att rapportera belastningen. Mer information om hur du använder kapacitets mått för att rapportera inläsning finns i Service Fabric Cluster Resource Manager-dokument för att [beskriva ditt kluster och dina](service-fabric-cluster-resource-manager-cluster-description.md) [mått och belastning](service-fabric-cluster-resource-manager-metrics.md).

## <a name="set-health-policies-for-automatic-upgrades"></a>Ange hälso principer för automatiska uppgraderingar
Du kan ange anpassade hälso principer för uppgradering av infrastrukturen. Om du har konfigurerat klustret till automatiska Fabric-uppgraderingar, kommer dessa principer att tillämpas på fas 1 av de automatiska Fabric-uppgraderingarna.
Om du har ställt in klustret för manuella Fabric-uppgraderingar, kommer dessa principer att tillämpas varje gång du väljer en ny version som aktiverar systemet för att starta uppgraderingen av Fabric i klustret. Om du inte åsidosätter principerna används standardvärdena.

Du kan ange anpassade hälso principer eller granska de aktuella inställningarna på bladet "Fabric Upgrade" genom att välja avancerade uppgraderings inställningar. Läs följande bild om hur du gör. 

![Hantera anpassade hälso principer][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>Anpassa infrastruktur inställningarna för klustret
Många olika konfigurations inställningar kan anpassas i ett kluster, till exempel Tillförlitlighets nivån för klustret och nodens egenskaper. Mer information finns i [Service Fabric Cluster Fabric-inställningar](service-fabric-cluster-fabric-settings.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Korrigera operativ systemet på klusternoderna
POA (patch Orchestration Application) är ett Service Fabric program som automatiserar operativ Systems korrigeringar på ett Service Fabric kluster utan drift avbrott. [Programmet för uppdaterings dirigering för Windows](service-fabric-patch-orchestration-application.md) kan distribueras i klustret för att installera uppdateringar på ett dirigerat sätt och samtidigt hålla tjänsterna tillgängliga hela tiden.


## <a name="next-steps"></a>Nästa steg
* Lär dig hur du anpassar några av [inställningarna för Service Fabric-klustrets infrastruktur resurser](service-fabric-cluster-fabric-settings.md)
* Lär dig hur du [skalar upp och ut ditt kluster](service-fabric-cluster-scale-up-down.md)
* Lär dig mer om [program uppgraderingar](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
