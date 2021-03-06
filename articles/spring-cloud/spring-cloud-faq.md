---
title: Vanliga frågor och svar om Azure våren Cloud | Microsoft Docs
description: I den här artikeln får du svar på vanliga frågor om Azure våren Cloud.
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 6835132192040b4b943b156fb78ae1547522be0c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229239"
---
# <a name="azure-spring-cloud-faq"></a>Vanliga frågor och svar om Azure våren Cloud

I den här artikeln får du svar på vanliga frågor om Azure våren Cloud. 

## <a name="general"></a>Allmänt

### <a name="why-azure-spring-cloud"></a>Varför Azure våren Cloud?

Azure våren Cloud tillhandahåller en PaaS (Platform as a Service) för vår moln utvecklare. Azure våren Cloud hanterar din program infrastruktur så att du kan fokusera på program kod och affärs logik. Kärn funktioner som är inbyggda i Azure våren Cloud inkluderar Eureka, konfigurations Server, tjänst register Server, pivotal Build Service, blå-grön distributioner med mera. Den här tjänsten gör det också möjligt för utvecklare att binda sina program till andra Azure-tjänster, till exempel Azure Cosmos DB, Azure Database for MySQL och Azure cache för Redis.

Azure våren Cloud förbättrar programdiagnostikens upplevelse för utvecklare och operatörer genom att integrera Azure Monitor, Application Insights och Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Vilka tjänst planer erbjuder Azure våren Cloud?

Azure våren Cloud erbjuder en service plan under för hands perioden.  En våren Cloud-distribution innehåller 16 vCPU kärnor och 32 GB minne.  Den övre kanten för varje mikrotjänst instans i en distribution är 4 vCPU kärnor med 8 GB minne.

Resource | Mängd
------- | -------
App-instanser per fjäder program | 20
Totalt antal App-instanser per Azure våren Cloud Service-instans | 500
Azure våren Cloud Service-instanser per region per prenumeration | 10
Beständiga volymer | 10 x 50 GBytes

_öppna ett [support ärende](https://azure.microsoft.com/support/faq/)\* för att höja gränsen._

Mer information finns i [vanliga frågor och svar om support för Azure](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Hur säkert är Azure våren Cloud?

Säkerhet och sekretess är bland de viktigaste prioriteringarna för Azure-och Azure våren Cloud-kunder. Azure hjälper till att säkerställa att endast kunder har åtkomst till program data, loggar eller konfigurationer genom att kryptera alla dessa data på ett säkert sätt. Alla tjänst instanser i Azure våren Cloud är isolerade från varandra.

Azure våren Cloud tillhandahåller fullständig SSL och certifikat hantering.

Viktiga säkerhets uppdateringar för OpenJDK och våren Cloud runtime används i Azure våren Cloud så snart som möjligt.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>I vilka regioner är Azure våren Cloud tillgängligt?

Östra USA, västra USA 2, Västeuropa och Sydostasien.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Vilka är de kända begränsningarna i Azure våren Cloud?

I för hands versionen har Azure våren Cloud följande kända begränsningar:

* `spring.application.name` kommer att åsidosättas av det program namn som används för att skapa varje program.
* `server.port` tillåts inte i konfigurations filen från git-lagrings platsen. Om du lägger till den i konfigurations filen återges troligen programmet inte kan komma åt från andra program eller Internet.
* Azure Portal-och Azure Resource Manager-mallarna stöder inte överföring av programpaket. Du kan bara överföra program paket genom att distribuera programmet via Azure CLI.
* Om du vill veta mer om kvot begränsningar, se [vilka tjänst prenumerationer erbjuder Azure våren Cloud?](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Hur kan jag ge feedback och rapportera problem?

Om du stöter på problem med Azure våren Cloud kan du skapa en [support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Om du vill skicka en funktions förfrågan eller lämna feedback går du till [Azure feedback](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>Utveckling

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Jag är en moln utvecklare som är våren men som är ny i Azure. Vad är det snabbaste sättet för mig att lära sig hur man utvecklar ett Azure våren Cloud-program?

För det snabbaste sättet att komma igång med Azure våren Cloud följer du anvisningarna i [snabb start: starta ett Azure våren Cloud-program med hjälp av Azure Portal](spring-cloud-quickstart-launch-app-portal.md).

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Vilken Java-körning stöder Azure våren Cloud?

Azure våren Cloud har stöd för Java 8 och 11.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Var kan jag se mina vår moln program loggar och mått?

Hitta mått på fliken Översikt över appen och fliken [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) .

Azure våren Cloud stöder export av vår moln program loggar och mät värden till Azure Storage, EventHub och [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). Tabell namnet i Log Analytics är *AppPlatformLogsforSpring*. Information om hur du aktiverar det finns i [Diagnostic Services](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Stöder Azure våren Cloud Distributed tracing?

Ja. Mer information finns i [Självstudier: använda distribuerad spårning med Azure våren Cloud](spring-cloud-tutorial-distributed-tracing.md).

### <a name="what-resource-types-does-service-binding-support"></a>Vilka resurs typer stöder tjänst bindning?

Tre tjänster stöds för närvarande: Azure Cosmos DB, Azure Database for MySQL och Azure cache för Redis.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Kan jag visa, lägga till eller flytta beständiga volymer inifrån mina program?

Ja.

## <a name="deployment"></a>Distribution

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Stöder Azure våren Cloud blå-grön distribution?
Ja. Mer information finns i [Konfigurera en mellanlagrings miljö](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Kan jag få åtkomst till Kubernetes för att ändra mina program behållare?

Nej.  Azure våren Cloud är en sammanfattning av utvecklaren från den underliggande arkitekturen, så att du kan koncentrera dig på program kod och affärs logik.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Har Azure våren Cloud stöd för att skapa behållare från källa?

Ja. Mer information finns i [starta ditt våren Cloud-program från käll koden](spring-cloud-launch-from-source.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Stöder Azure våren Cloud automatisk skalning i App-instanser?

Nej.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Vilka är bästa praxis för att migrera befintliga våren Cloud-mikrotjänster till Azure våren Cloud?

När du migrerar befintliga våren Cloud-mikrotjänster till Azure våren-molnet är det en bra idé att Observera följande bästa praxis:
* Alla program beroenden måste lösas.
* Förbered dina konfigurations poster, miljövariabler och JVM-parametrar så att du kan jämföra dem med distributionen i Azure våren Cloud.
* Om du vill använda tjänst bindningen går du igenom dina Azure-tjänster och kontrollerar att du har angett rätt åtkomst behörighet.
* Vi rekommenderar att du tar bort eller inaktiverar inbäddade tjänster som kan vara i konflikt med tjänster som hanteras av Azure våren Cloud, till exempel tjänst identifierings tjänsten, konfigurations servern och så vidare.
* Vi rekommenderar att du använder officiella, stabila, vridnings fjäder-bibliotek. Icke-officiella, beta-eller förgrenings versioner av pivotal våren-bibliotek har inget stöd för service avtal (SLA).

Efter migreringen övervakar du dina CPU/RAM-mått och nätverks trafik för att säkerställa att program instanserna skalas på rätt sätt.

## <a name="next-steps"></a>Nästa steg

Om du har fler frågor kan du läsa [fel söknings guiden för Azure våren Cloud](spring-cloud-troubleshoot.md).
