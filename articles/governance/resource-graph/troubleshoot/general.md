---
title: Felsöka vanliga fel
description: 'Lär dig hur du felsöker problem med de olika SDK: erna när du frågar Azure-resurser med Azure Resource Graph.'
ms.date: 10/18/2019
ms.topic: troubleshooting
ms.openlocfilehash: f881db4f75bcee8c13221717596442ac29a4b1ac
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74303908"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Felsöka fel med Azure Resource Graph

Du kan stöta på fel när du frågar Azure-resurser med Azure Resource Graph. I den här artikeln beskrivs olika fel som kan uppstå och hur du löser dem.

## <a name="finding-error-details"></a>Hitta fel information

De flesta fel är resultatet av ett problem när du kör en fråga med Azure Resource Graph. När en fråga Miss lyckas innehåller SDK information om den misslyckade frågan. Den här informationen anger problemet så att det kan åtgärdas och en senare fråga lyckas.

## <a name="general-errors"></a>Allmänna fel

### <a name="toomanysubscription"></a>Scenario: för många prenumerationer

#### <a name="issue"></a>Problem

Kunder med till gång till fler än 1000 prenumerationer, inklusive prenumerationer mellan klienter med [Azure Lighthouse](../../../lighthouse/overview.md), kan inte hämta data över alla prenumerationer i ett enda anrop till Azures resurs diagram.

#### <a name="cause"></a>Orsak

Azure CLI och PowerShell vidarebefordrar bara de första 1000 prenumerationerna till Azure Resource Graph. REST API för Azure Resource Graph accepterar maximalt antal prenumerationer som frågan kan utföras på.

#### <a name="resolution"></a>Lösning

Batch-begäranden för frågan med en delmängd av prenumerationerna kvar under prenumerations gränsen på 1000. Lösningen använder **prenumerations** parametern i PowerShell.

```azurepowershell-interactive
# Replace this query with your own
$query = 'Resources | project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

### <a name="rest-contenttype"></a>Scenario: REST-huvud för innehålls typ som inte stöds

#### <a name="issue"></a>Problem

Kunder som frågar Azure Resource Graph REST API får ett _500_ -svar (internt Server fel) returnerat.

#### <a name="cause"></a>Orsak

Azures resurs diagram REST API bara stöd för en `Content-Type` av **programmet/JSON**. Vissa REST verktyg eller agenter som standard är **text/plain**, vilket inte stöds av REST API.

#### <a name="resolution"></a>Lösning

Kontrol lera att verktyget eller agenten som du använder för att fråga Azure Resource Graph har REST API huvud `Content-Type` konfigurerat för **Application/JSON**.

### <a name="rest-403"></a>Scenario: ingen Läs behörighet för alla prenumerationer i listan

#### <a name="issue"></a>Problem

Kunder som uttryckligen skickar en lista över prenumerationer med en Azure-resurs Graph-fråga får ett _403_ -svar (förbjuden).

#### <a name="cause"></a>Orsak

Om kunden inte har Läs behörighet till alla angivna prenumerationer, nekas begäran på grund av brist på rätt behörighet.

#### <a name="resolution"></a>Lösning

Inkludera minst en prenumeration i prenumerations listan som kunden som kör frågan har minst Läs behörighet till. Mer information finns i [behörigheter i Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph).

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

- Få svar från Azure-experter via [Azure-forum](https://azure.microsoft.com/support/forums/).
- Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
- Om du behöver mer hjälp kan du skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.