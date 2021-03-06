---
title: Konfigurera Azure Monitor för behållare agent data insamling | Microsoft Docs
description: I den här artikeln beskrivs hur du kan konfigurera Azure Monitor för behållare agent för att styra logg insamling för STDOUT/stderr och miljövariabler.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 0d654dc05668a71b0fe69de32e5c09f8936951f8
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951589"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Konfigurera agent data insamling för Azure Monitor för behållare

Azure Monitor för behållare samlar in STDOUT-, stderr-och miljövariabler från behållar arbets belastningar som distribueras till hanterade Kubernetes-kluster som finns i Azure Kubernetes service (AKS) från behållarens agent. Du kan konfigurera inställningar för data insamling för agenter genom att skapa en anpassad Kubernetes-ConfigMaps för att styra den här upplevelsen. 

Den här artikeln visar hur du skapar ConfigMap och konfigurerar data insamling utifrån dina krav.

## <a name="configmap-file-settings-overview"></a>Översikt över ConfigMap-fil inställningar

En mall ConfigMap-fil tillhandahålls som gör att du enkelt kan redigera den med dina anpassningar utan att behöva skapa den från grunden. Innan du börjar bör du gå igenom Kubernetes-dokumentationen om [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) och bekanta dig med hur du skapar, konfigurerar och distribuerar ConfigMaps. Detta gör att du kan filtrera stderr och STDOUT per namnrymd eller över hela klustret, och miljövariabler för alla behållare som körs i alla poddar/noder i klustret.

>[!IMPORTANT]
>Den lägsta agent version som stöds för att samla in STDOUT-, stderr-och miljövariabler från behållar arbets belastningar är ciprod06142019 eller senare. Om du vill verifiera agent versionen går du till fliken **Node** och väljer en nod. i rutan Egenskaper noterar du värdet för egenskapen för **agentens avbildnings tag** . För ytterligare information om agent versionerna och vad som ingår i varje utgåva, se [agent viktig](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)information.

### <a name="data-collection-settings"></a>Inställningar för data insamling

Följande är de inställningar som kan konfigureras för att styra data insamling.

|Nyckel |Datatyp |Värde |Beskrivning |
|----|----------|------|------------|
|`schema-version` |Sträng (Skift läges känslig) |v1 |Det här är den schema version som används av agenten vid parsning av den här ConfigMap. Schema version som stöds för närvarande är v1. Det finns inte stöd för att ändra det här värdet och kommer att avvisas när ConfigMap utvärderas.|
|`config-version` |Sträng | | Stöder möjlighet att hålla koll på den här konfigurations filens version i käll kontroll systemet/lagrings platsen. Maximalt antal tillåtna tecken är 10 och alla andra tecken trunkeras. |
|`[log_collection_settings.stdout] enabled =` |Boolesk | sant eller falskt | Kontrollerar om STDOUT container logg samling är aktive rad. När värdet är inställt på `true` och inga namn områden utesluts för STDOUT logg samling (`log_collection_settings.stdout.exclude_namespaces` inställningen nedan) samlas STDOUT-loggar in från alla behållare i alla poddar/noder i klustret. Om inget värde anges i ConfigMaps är standardvärdet `enabled = true`. |
|`[log_collection_settings.stdout] exclude_namespaces =`|Sträng | Kommaavgränsad matris |Matris med Kubernetes-namnområden som StdOut-loggar inte ska samlas in för. Den här inställningen gäller endast om `log_collection_settings.stdout.enabled` är inställt på `true`. Om inget värde anges i ConfigMap är standardvärdet `exclude_namespaces = ["kube-system"]`.|
|`[log_collection_settings.stderr] enabled =` |Boolesk | sant eller falskt |Anger om stderr container logg samling är aktive rad. När värdet är inställt på `true` och inga namn områden exkluderas för STDOUT logg samling (`log_collection_settings.stderr.exclude_namespaces` inställningen) samlas stderr-loggar in från alla behållare i alla poddar/noder i klustret. Om inget värde anges i ConfigMaps är standardvärdet `enabled = true`. |
|`[log_collection_settings.stderr] exclude_namespaces =` |Sträng |Kommaavgränsad matris |Matris med Kubernetes-namnområden som stderr-loggar inte ska samlas in för. Den här inställningen gäller endast om `log_collection_settings.stdout.enabled` är inställt på `true`. Om inget värde anges i ConfigMap är standardvärdet `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` |Boolesk | sant eller falskt | Den här inställningen styr en samling av miljövariabler över alla poddar/noder i klustret och standardvärdet är `enabled = true` när det inte anges i ConfigMaps. Om samlingen av miljövariabler är globalt aktive rad, kan du inaktivera den för en speciell behållare genom att ställa in miljövariabeln `AZMON_COLLECT_ENV` på **false** antingen med en Dockerfile-inställning eller i [konfigurations filen för Pod](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) under avsnittet **miljö:** . Om insamlingen av miljövariabler är globalt inaktive rad kan du inte aktivera samling för en speciell behållare (det vill säga den enda åsidosättning som kan tillämpas på behållar nivån är att inaktivera samlingen när den redan är aktive rad globalt.). |

ConfigMaps är en global lista och det kan bara finnas en ConfigMap som tillämpas på agenten. Det går inte att ha en annan ConfigMaps över samlingarna.

## <a name="configure-and-deploy-configmaps"></a>Konfigurera och distribuera ConfigMaps

Utför följande steg för att konfigurera och distribuera din ConfigMap-konfigurationsfil till klustret.

1. [Hämta](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) mallen ConfigMap yaml File och spara den som container-AZM-MS-agentconfig. yaml.  

2. Redigera ConfigMap yaml-filen med dina anpassningar för att samla in STDOUT-, stderr-och/eller miljövariabler.

    - Om du vill undanta vissa namn rymder för STDOUT-logg samling konfigurerar du nyckeln/värdet med hjälp av följande exempel: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    
    - Om du vill inaktivera en miljö variabel samling för en speciell behållare ställer du in nyckel/värde-`[log_collection_settings.env_var] enabled = true` så att variabel samlingen aktive ras globalt och följer sedan stegen [här](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) för att slutföra konfigurationen av den angivna behållaren.
    
    - Om du vill inaktivera stderr-logg samling i hela klustret konfigurerar du nyckeln/värdet med hjälp av följande exempel: `[log_collection_settings.stderr] enabled = false`.

3. Skapa ConfigMap genom att köra följande kubectl-kommando: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Exempel: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    Konfigurations ändringen kan ta några minuter innan den börjar gälla, och alla omsagent-poddar i klustret kommer att startas om. Omstarten är en rullande omstart för alla omsagent-poddar, inte alla omstart på samma tidpunkt. När omstarterna är klara visas ett meddelande som liknar följande och som innehåller resultatet: `configmap "container-azm-ms-agentconfig" created`.

## <a name="verify-configuration"></a>Verifiera konfigurationen 

Verifiera att konfigurationen har tillämpats genom att använda följande kommando för att granska loggarna från en agent pod: `kubectl logs omsagent-fdf58 -n=kube-system`. Om det finns konfigurations fel från omsagent-poddar visas fel som liknar följande i utdata:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Fel som rör tillämpning av konfigurations ändringar är också tillgängliga för granskning. Följande alternativ är tillgängliga för att utföra ytterligare fel sökning av konfigurations ändringar:

- Från en agents Pod loggar med samma `kubectl logs`-kommando. 

- Från Live-loggar. Live-loggar visar fel som liknar följande:

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- Från tabellen **KubeMonAgentEvents** i din Log Analytics-arbetsyta. Data skickas varje timme med *fel* allvarlighets grad för konfigurations fel. Om det inte finns några fel innehåller posten i tabellen data med allvarlighets grad *information*som inte rapporterar några fel. Egenskapen **Tags** innehåller mer information om Pod och behållar-ID: t där felet inträffade och även den första förekomsten, senaste förekomst och antal under den senaste timmen.

Fel förhindrar omsagent från att parsa filen, vilket gör att den startas om och använder standard konfigurationen. När du har korrigerat felen i ConfigMap sparar du yaml-filen och använder den uppdaterade ConfigMaps genom att köra kommandot: `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="applying-updated-configmap"></a>Använder uppdaterad ConfigMap

Om du redan har distribuerat en ConfigMap till klustret och du vill uppdatera det med en nyare konfiguration kan du redigera ConfigMap-filen som du tidigare har använt och sedan använda samma kommando som tidigare, `kubectl apply -f <configmap_yaml_file.yaml`.

Konfigurations ändringen kan ta några minuter innan den börjar gälla, och alla omsagent-poddar i klustret kommer att startas om. Omstarten är en rullande omstart för alla omsagent-poddar, inte alla omstart på samma tidpunkt. När omstarterna är klara visas ett meddelande som liknar följande och som innehåller resultatet: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Verifierar schema version

Konfigurations schema versioner som stöds är tillgängliga som Pod-anteckning (schema versioner) på omsagent-pod. Du kan se dem med följande kubectl-kommando: `kubectl describe pod omsagent-fdf58 -n=kube-system`

Utdata ser ut ungefär så här med antecknings schema versioner:

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>Nästa steg

- Azure Monitor för behållare innehåller inte en fördefinierad uppsättning aviseringar. Mer information om hur du skapar rekommenderade aviseringar för hög processor-och minnes användning finns i avsnittet [skapa prestanda aviseringar med Azure Monitor för behållare](container-insights-alerts.md) som stöder DevOps eller operativa processer och procedurer

- När övervakning har Aktiver ATS för att samla in hälso-och resursutnyttjande för AKS eller hybrid kluster och arbets belastningar som körs på dem, lär [du dig hur du använder](container-insights-analyze.md) Azure Monitor för behållare.

- Visa [exempel på logg frågor](container-insights-log-search.md#search-logs-to-analyze-data) för att se fördefinierade frågor och exempel för att utvärdera eller anpassa för aviseringar, visualisering eller analys av klustren.
