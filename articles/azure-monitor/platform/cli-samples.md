---
title: Snabb starts exempel för Azure Monitor CLI
description: 'Exempel på CLI-kommandon för Azure Monitor-funktioner. Azure Monitor är en Microsoft Azure tjänst som gör att du kan skicka aviseringar, anropa webb-URL: er baserat på värden för konfigurerade telemetridata och Autoskala Cloud Services, Virtual Machines och Web Apps.'
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 05/16/2018
ms.openlocfilehash: 0def528e0c432ecbc706622f0b8c29c2d610c663
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893543"
---
# <a name="azure-monitor-cli-quick-start-samples"></a>Snabb starts exempel för Azure Monitor CLI
Den här artikeln visar exempel kommandon för kommando rads gränssnitt (CLI) som hjälper dig att komma åt Azure Monitor-funktioner. Med Azure Monitor kan du Autoskala Cloud Services, Virtual Machines och Web Apps och skicka aviseringar eller anropa webb-URL: er baserat på värden för konfigurerade telemetridata.

## <a name="prerequisites"></a>Krav

Om du inte redan har installerat Azure CLI följer du anvisningarna för att [Installera Azure CLI](/cli/azure/install-azure-cli). Du kan också använda [Azure Cloud Shell](/azure/cloud-shell) för att köra CLI som en interaktiv upplevelse i webbläsaren. Se en fullständig referens för alla tillgängliga kommandon i [Azure Monitor CLI-referensen](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). 

## <a name="log-in-to-azure"></a>Logga in på Azure
Det första steget är att logga in på ditt Azure-konto.

```azurecli
az login
```

När du har kört det här kommandot måste du logga in via instruktionerna på skärmen. Alla kommandon fungerar i kontexten för din standard prenumeration.

Om du vill visa information om den aktuella prenumerationen använder du följande kommando.

```azurecli
az account show
```

Använd följande kommando om du vill ändra arbets kontexten till en annan prenumeration.

```azurecli
az account set -s <Subscription ID or name>
```

Om du vill visa en lista över alla Azure Monitor kommandon som stöds utför du följande.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Visa aktivitets loggen för en prenumeration

Gör följande om du vill visa en lista över aktivitets logg händelser.

```azurecli
az monitor activity-log list
```

Prova följande om du vill visa alla tillgängliga alternativ.

```azurecli
az monitor activity-log list -h
```

Här är ett exempel på en lista över loggar av en resourceGroup

```azurecli
az monitor activity-log list --resource-group <group name>
```

Exempel för att visa en lista över loggar per anropare

```azurecli
az monitor activity-log list --caller myname@company.com
```

Exempel för att visa en lista över loggar efter anropare på en resurs typ inom ett datum intervall

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Arbeta med aviseringar 
> [!NOTE]
> Endast aviseringar (klassisk) stöds i CLI för tillfället. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Hämta varnings regler (klassiska) i en resurs grupp

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Skapa en regel för mått varningar (klassisk)

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Ta bort en varnings regel (klassisk)

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Logg profiler

Använd informationen i det här avsnittet för att arbeta med logg profiler.

### <a name="get-a-log-profile"></a>Hämta en logg profil

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Lägg till en logg profil med kvarhållning

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Lägg till en logg profil med kvarhållning och EventHub

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Ta bort en logg profil

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Diagnostik

Använd informationen i det här avsnittet för att arbeta med diagnostikinställningar.

### <a name="get-a-diagnostic-setting"></a>Hämta en diagnostisk inställning

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-setting"></a>Skapa en diagnostisk inställning 

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <storage account ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

### <a name="delete-a-diagnostic-setting"></a>Ta bort en diagnostisk inställning

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Automatisk skalning

Använd informationen i det här avsnittet för att arbeta med inställningarna för autoskalning. Du måste ändra dessa exempel.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Hämta inställningar för autoskalning för en resurs grupp

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Hämta inställningar för autoskalning efter namn i en resurs grupp

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Ange inställningar för autoskalning

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```

