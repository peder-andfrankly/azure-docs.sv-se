---
title: Azure Monitor för behållare vanliga frågor och svar | Microsoft Docs
description: Azure Monitor for containers är en lösning som övervakar hälso tillståndet för dina AKS-kluster och Container Instances i Azure. I den här artikeln besvaras vanliga frågor.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: d3779a2d48db82bfccdc0f047119a36ef56c3bdf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73477416"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Azure Monitor för behållare vanliga frågor och svar

Microsoft FAQ (vanliga frågor och svar) är en lista över vanliga frågor om Azure Monitor för behållare. Om du har ytterligare frågor om lösningen går du till [diskussions forumet](https://feedback.azure.com/forums/34192--general-feedback) och publicerar dina frågor. När en fråga ofta är tillfrågad, lägger vi till den i den här artikeln så att den snabbt och enkelt kan hittas.

## <a name="can-i-view-metrics-collected-in-grafana"></a>Kan jag visa mått som samlats in i Grafana?

Azure Monitor for containers stöder visning av mått som lagras i din Log Analytics arbets yta i Grafana-instrumentpaneler. Vi har angett en mall som du kan ladda ned från Grafana för [instrument panelen](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) för att komma igång och referera till att hjälpa dig att fråga efter ytterligare data från de övervakade klustren för att visualisera anpassade Grafana-instrument paneler. 

## <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Kan jag övervaka mitt AKS-kluster med Azure Monitor för behållare?

Azure Monitor för behållare stöder övervakning av arbets belastningar som distribueras till AKS-motorn (tidigare ACS-motor) som finns på Azure. Mer information och en översikt över de steg som krävs för att aktivera övervakning för det här scenariot finns i [använda Azure Monitor för behållare för AKS-Engine](https://github.com/microsoft/OMS-docker/tree/aks-engine).

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Varför visas inte data i min Log Analytics-arbetsyta?

Om du inte kan se några data i Log Analytics arbets ytan vid en viss tid varje dag, kan du ha uppnått standard gränsen på 500 MB eller den dagliga begränsningen för att styra mängden data som ska samlas in dagligen. När gränsen uppfylls för dagen stoppas data insamlingen och återupptas bara på nästa dag. Information om hur du granskar din data användning och uppdaterar till en annan pris nivå baserat på dina förväntade användnings mönster finns i [logga data användning och kostnad](../platform/manage-cost-storage.md). 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Vilka är behållar tillstånden angivna i ContainerInventory-tabellen?

Tabellen ContainerInventory innehåller information om både stoppade och pågående behållare. Tabellen fylls i av ett arbets flöde i agenten som frågar Docker efter alla behållare (körs och stoppas) och vidarebefordrar dessa data till Log Analytics arbets ytan.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Hur gör jag för att lösa *saknade prenumerations registrerings* fel?

Om du får ett fel meddelande om att **prenumerations registrering saknas för Microsoft. OperationsManagement**kan du lösa det genom att registrera resurs leverantören **Microsoft. OperationsManagement** i prenumerationen där arbets ytan definieras. Dokumentationen för hur du kan göra detta finns [här](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Finns det stöd för RBAC-aktiverade AKS-kluster?

Lösningen för övervakning av behållare stöder inte RBAC, men den stöds med Azure Monitor för behållare. Sidan lösnings information visar kanske inte rätt information i bladet som visar data för de här klustren.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Hur gör jag för att aktivera logg insamling för behållare i namn området Kube-system via Helm?

Logg samlingen från behållare i Kube-systemets namnrymd är inaktive rad som standard. Logg insamling kan aktive ras genom att ställa in en miljö variabel på omsagent. Mer information finns på sidan [Azure Monitor for containers](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) GitHub. 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Hur gör jag för att du uppdatera omsagent till den senaste versionen?

Information om hur du uppgraderar agenten finns i [agent hantering](container-insights-manage-agent.md).

## <a name="how-do-i-enable-multi-line-logging"></a>Hur gör jag för att aktivera loggning på flera rader?

För närvarande har Azure Monitor för behållare inte stöd för flera rader, men det finns lösningar som är tillgängliga. Du kan konfigurera alla tjänster som ska skrivas i JSON-format och sedan Docker/Moby att skriva dem som en enda rad.

Du kan till exempel figursätta loggen som ett JSON-objekt, som du ser i exemplet nedan för ett exempel på Node. js-program:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Dessa data kommer att se ut som i följande exempel i Azure Monitor för loggar när du frågar efter den:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

En detaljerad översikt över problemet finns i följande [GitHub-länk](https://github.com/moby/moby/issues/22920).

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Hur gör jag för att lösa Azure AD-fel när jag aktiverar Live-loggar? 

Följande fel kan visas: svars-URL: en som **anges i begäran matchar inte de svars-URL: er som har kon figurer ATS för programmet: < program-ID\>** . Lösningen för att lösa problemet finns i artikeln [så här visar du behållar data i real tid med Azure Monitor för behållare](container-insights-livedata-setup.md#configure-ad-integrated-authentication). 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Varför kan jag inte uppgradera klustret efter onboarding?

Om du när du har aktiverat Azure Monitor för behållare för ett AKS-kluster tar du bort arbets ytan Log Analytics som klustret skickade data till, vid försök att uppgradera klustret. För att undvika detta måste du inaktivera övervakning och sedan återaktivera den till en annan giltig arbets yta i din prenumeration. När du försöker utföra kluster uppgraderingen igen bör den bearbetas och slutföras.  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Vilka portar och domäner behöver jag öppna/vitlista för agenten?

Se [nätverks brand Väggs kraven](container-insights-onboard.md#network-firewall-requirements) för proxy-och brand Väggs konfigurations information som krävs för behållarens agent med Azure, Azure amerikanska myndigheter och moln i Azure Kina.

## <a name="next-steps"></a>Nästa steg

Om du vill börja övervaka ditt AKS-kluster granskar du [hur du kan publicera Azure Monitor för behållare](container-insights-onboard.md) för att förstå kraven och tillgängliga metoder för att aktivera övervakning. 
