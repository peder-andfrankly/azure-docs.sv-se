---
title: Förstå frågespråket
description: Beskriver resurs diagram tabeller och tillgängliga Kusto data typer, operatorer och funktioner som kan användas med Azure Resource Graph.
ms.date: 12/05/2019
ms.topic: conceptual
ms.openlocfilehash: a3503ce8d83b5bd47872db4b1de0eadb88be432c
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851221"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Förstå frågespråket i Azure Resource Graph

Frågespråket för Azure Resource Graph stöder ett antal operatorer och funktioner. Varje arbete och drift baserat på [KQL (Kusto Query Language)](/azure/kusto/query/index). Om du vill veta mer om frågespråket som används av resurs diagrammet börjar du med [självstudien för KQL](/azure/kusto/query/tutorial).

Den här artikeln beskriver de språk komponenter som stöds av resurs diagram:

- [Resurs diagram tabeller](#resource-graph-tables)
- [Språk element i KQL som stöds](#supported-kql-language-elements)
- [Escape-tecken](#escape-characters)

## <a name="resource-graph-tables"></a>Resurs diagram tabeller

Resurs diagram innehåller flera tabeller för de data som lagras om resurs typerna Resource Manager och deras egenskaper. Dessa tabeller kan användas med `join`-eller `union`-operatörer för att hämta egenskaper från relaterade resurs typer. Här är listan över tabeller som är tillgängliga i resurs diagram:

|Resurs diagram tabeller |Beskrivning |
|---|---|
|Resurser |Standard tabellen om ingen har definierats i frågan. De flesta resurs typer och egenskaper för Resource Manager finns här. |
|ResourceContainers |Inkluderar prenumeration (i förhands granskning--`Microsoft.Resources/subscriptions`) och resurs typ och data för resurs grupp (`Microsoft.Resources/subscriptions/resourcegroups`). |
|AlertsManagementResources |Innehåller resurser som är _relaterade_ till `Microsoft.AlertsManagement`. |
|SecurityResources |Innehåller resurser som är _relaterade_ till `Microsoft.Security`. |

> [!NOTE]
> _Resurser_ är standard tabellen. När du frågar _resurser_ -tabellen, behöver du inte ange tabell namnet om inte `join` eller `union` används. Den rekommenderade metoden är dock att alltid inkludera den första tabellen i frågan.

Använd resurs diagram Utforskaren i portalen för att identifiera vilka resurs typer som är tillgängliga i varje tabell. Alternativt kan du använda en fråga som `<tableName> | distinct type` för att hämta en lista över resurs typer som den aktuella resurs diagram tabellen stöder som finns i din miljö.

Följande fråga visar ett enkelt `join`. Frågeresultatet blandar samman kolumnerna och alla duplicerade kolumn namn från den kopplade tabellen, _ResourceContainers_ i det här exemplet, läggs till med **1**. Eftersom _ResourceContainers_ -tabellen har typer för både prenumerationer och resurs grupper kan du använda någon av typerna för att ansluta till _resursen från resurs_ tabellen.

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

Följande fråga visar en mer komplex användning av `join`. Frågan begränsar den kopplade tabellen till prenumerations resurser och `project` att bara inkludera det ursprungliga fältet _subscriptionId_ och _namn_ fältet har bytt namn till _undernamn_. Fältet Byt namn undviker `join` att lägga till det som _name1_ eftersom fältet redan finns i _resurser_. Den ursprungliga tabellen filtreras med `where` och följande `project` innehåller kolumner från båda tabellerna. Frågeresultatet är ett enda nyckel valv som visar typ, namnet på nyckel valvet och namnet på den prenumeration som det finns i.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> När du begränsar `join` resultat med `project`måste den egenskap som används av `join` relatera de två tabellerna, _subscriptionId_ i ovanstående exempel, måste inkluderas i `project`.

## <a name="supported-kql-language-elements"></a>Språk element i KQL som stöds

Resurs diagram stöder alla KQL- [datatyper](/azure/kusto/query/scalar-data-types/), [skalära funktioner](/azure/kusto/query/scalarfunctions), [skalära operatorer](/azure/kusto/query/binoperators)och [agg regerings funktioner](/azure/kusto/query/any-aggfunction). Vissa [tabell operatörer](/azure/kusto/query/queries) stöds av resurs diagram, varav vissa är olika beteenden.

### <a name="supported-tabulartop-level-operators"></a>Operatorer för huvud-/topp nivå som stöds

Här är listan över KQL tabell operatörer som stöds av resurs diagram med vissa exempel:

|KQL |Exempel fråga för resurs diagram |Anteckningar |
|---|---|---|
|[antal](/azure/kusto/query/countoperator) |[Räkna nyckel valv](../samples/starter.md#count-keyvaults) | |
|[kontrollstämpel](/azure/kusto/query/distinctoperator) |[Visa distinkta värden för ett visst alias](../samples/starter.md#distinct-alias-values) | |
|[batteri](/azure/kusto/query/extendoperator) |[Antal virtuella datorer efter OS-typ](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[Nyckel valv med prenumerations namn](../samples/advanced.md#join) |Join-varianter som stöds: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [Inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Gräns på 3 `join` i en enskild fråga. Anpassade kopplings strategier, till exempel sändnings anslutning, är inte tillåtna. Kan användas i en enskild tabell eller mellan _resurserna_ och _ResourceContainers_ -tabellerna. |
|[gränserna](/azure/kusto/query/limitoperator) |[Lista över alla offentliga IP-adresser](../samples/starter.md#list-publicip) |Synonymer för `take` |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Äldre operatör använder `mv-expand` i stället. _ROWLIMIT_ max 400. Standardvärdet är 128. |
|[MV-expandera](/azure/kusto/query/mvexpandoperator) |[Lista Cosmos DB med vissa Skriv platser](../samples/advanced.md#mvexpand-cosmosdb) |_ROWLIMIT_ max 400. Standardvärdet är 128. |
|[för](/azure/kusto/query/orderoperator) |[Lista över resurser sorterade efter namn](../samples/starter.md#list-resources) |Synonymer för `sort` |
|[projektfilerna](/azure/kusto/query/projectoperator) |[Lista över resurser sorterade efter namn](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[Ta bort kolumner från resultat](../samples/advanced.md#remove-column) | |
|[ordning](/azure/kusto/query/sortoperator) |[Lista över resurser sorterade efter namn](../samples/starter.md#list-resources) |Synonymer för `order` |
|[sammanfatta](/azure/kusto/query/summarizeoperator) |[Antal Azure-resurser](../samples/starter.md#count-resources) |Endast förenklad första sidan |
|[take](/azure/kusto/query/takeoperator) |[Lista över alla offentliga IP-adresser](../samples/starter.md#list-publicip) |Synonymer för `limit` |
|[översta](/azure/kusto/query/topoperator) |[Visning av de första fem virtuella datorerna efter namn och OS-typ](../samples/starter.md#show-sorted) | |
|[union](/azure/kusto/query/unionoperator) |[Kombinera resultat från två frågor till ett enda resultat](../samples/advanced.md#unionresults) |Enskild tabell tillåts: _T_ `| union` \[`kind=` `inner`\|`outer`\] \[`withsource=`_columnName_\] _Table_. Gräns på 3 `union` ben i en enda fråga. Fuzzy-upplösning för `union`s ben tabeller är inte tillåtet. Kan användas i en enskild tabell eller mellan _resurserna_ och _ResourceContainers_ -tabellerna. |
|[vilken](/azure/kusto/query/whereoperator) |[Visning av resurser med lagring](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Escape-tecken

Vissa egenskaps namn, till exempel sådana som innehåller en `.` eller `$`, måste omslutas eller undantas i frågan, eller egenskaps namnet tolkas felaktigt och ger inte det förväntade resultatet.

- `.` – rad brytning av egenskaps namnet: `['propertyname.withaperiod']`
  
  Exempel fråga som radbryter egenskapen _OData. Type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` – undantar tecknen i egenskaps namnet. Vilket escape-tecken som används beror på vilket Shell-resurs diagram som körs från.

  - **bash** - `\`

    Exempel fråga som utrymningr egenskapen _\$typ_ i bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** – escape inte `$` tecken.

  - **PowerShell** - ``` ` ```

    Exempel fråga som utrymningr egenskapen _\$typ_ i PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Nästa steg

- Se språket som används i [Start frågor](../samples/starter.md).
- Se avancerade användnings områden i [avancerade frågor](../samples/advanced.md).
- Lär dig mer om hur du [utforskar resurser](explore-resources.md).