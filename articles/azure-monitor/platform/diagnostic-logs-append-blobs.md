---
title: Förbered för format ändring till Azure Monitor resurs loggar
description: Beskriver effekten och hur du uppdaterar ditt verktyg för att hantera de nya Azure-resursposterna som ändrades till att använda tilläggs-blobbar den 1 november 2018.
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: dc7fd8916f356414437d4def21f26f0b651ee76f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893526"
---
# <a name="prepare-for-format-change-to-azure-monitor-resource-logs-archived-to-a-storage-account"></a>Förbered för format ändring till Azure Monitor resurs loggar arkiverade på ett lagrings konto

> [!WARNING]
> Om du skickar [Azures resurs loggar eller mått till ett lagrings konto med hjälp av resurs diagnostikinställningar](./../../azure-monitor/platform/archive-diagnostic-logs.md) eller [aktivitets loggar till ett lagrings konto med hjälp av logg profiler](./../../azure-monitor/platform/archive-activity-log.md), kommer formatet för data i lagrings kontot att ändras till JSON-rader på nov. 1 2018. Anvisningarna nedan beskriver effekten och hur du uppdaterar ditt verktyg för att hantera det nya formatet. 
>
> 

## <a name="what-is-changing"></a>Vad som ändras

Azure Monitor erbjuder en funktion som gör att du kan skicka resurs diagnostikdata och aktivitets logg data till ett Azure Storage-konto, Event Hubs namnrymd eller till en Log Analytics arbets yta i Azure Monitor. För att lösa ett system prestanda problem, den **1 November 2018 vid 12:00 midnatt UTC** , kommer formatet för logg data att skickas till Blob Storage att ändras. Om du har verktyg som läser data från Blob Storage måste du uppdatera verktyget för att förstå det nya data formatet.

* Den 1 november 2018 vid 12:00 midnatt UTC kommer BLOB-formatet att ändras till [JSON-linjer](http://jsonlines.org/). Det innebär att varje post avgränsas med en ny rad, utan matris för yttre poster och inga kommatecken mellan JSON-poster.
* BLOB-formatet ändras för alla diagnostikinställningar i alla prenumerationer på en gång. Den första PT1H. JSON-filen som genereras för den 1 november kommer att använda det nya formatet. Blob-och container namnen förblir desamma.
* Att ställa in en diagnostisk inställning mellan nu och den 1 november fortsätter att generera data i det aktuella formatet fram till 1 november.
* Den här ändringen görs samtidigt i alla offentliga moln regioner. Ändringen görs inte i Microsoft Azure som drivs av 21Vianet, Azure Germany eller Azure Government moln ännu.
* Den här ändringen påverkar följande data typer:
  * [Azure resurs resurs loggar](archive-diagnostic-logs.md) ([Se lista över resurser här](diagnostic-logs-schema.md))
  * [Azure-resursens mått exporteras av diagnostikinställningar](diagnostic-settings.md)
  * [Azures aktivitets logg data exporteras av logg profiler](archive-activity-log.md)
* Den här ändringen påverkar inte:
  * Nätverks flödes loggar
  * Azures tjänst loggar är inte tillgängliga via Azure Monitor än (till exempel Azure App Service resurs loggar, lagrings analys loggar)
  * Routning av Azures resurs loggar och aktivitets loggar till andra destinationer (Event Hubs, Log Analytics)

### <a name="how-to-see-if-you-are-impacted"></a>Så här ser du om du påverkas

Du påverkas bara av den här ändringen om du:
1. Skickar loggdata till ett Azure Storage-konto med hjälp av en inställning för resurs diagnos och
2. Har verktyg som är beroende av JSON-strukturen för dessa loggar i lagring.
 
För att identifiera om du har inställningar för resurs diagnostik som skickar data till ett Azure Storage-konto, kan du gå till **övervaknings** avsnittet i portalen, klicka på **diagnostikinställningar**och identifiera alla resurser som har **diagnostisk status** inställd på **aktive rad**:

![Bladet Azure Monitor diagnostikinställningar](./media/diagnostic-logs-append-blobs/portal-diag-settings.png)

Om diagnostisk status är aktive rad har du en aktiv diagnostisk inställning för den resursen. Klicka på resursen för att se om några diagnostikinställningar skickar data till ett lagrings konto:

![Lagrings konto aktiverat](./media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

Om du har resurser som skickar data till ett lagrings konto med hjälp av dessa resurs diagnostikinställningar, kommer formatet på data i det lagrings kontot att påverkas av den här ändringen. Om du inte har anpassat verktyg som påverkar dessa lagrings konton påverkas inte format ändringen.

### <a name="details-of-the-format-change"></a>Information om format ändringen

Det aktuella formatet för PT1H. JSON-filen i Azure Blob Storage använder en JSON-matris med poster. Här är ett exempel på en logg fil för nyckel valvet:

```json
{
    "records": [
        {
            "time": "2016-01-05T01:32:01.2691226Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "78",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        },
        {
            "time": "2016-01-05T01:33:56.5264523Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "83",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        }
    ]
}
```

Det nya formatet använder [JSON-linjer](http://jsonlines.org/), där varje händelse är en rad och rad matnings tecknet indikerar en ny händelse. Så här ser exemplet ovan ut i filen PT1H. JSON efter ändringen:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Det nya formatet gör det möjligt Azure Monitor att skicka loggfiler med hjälp av [bifogade blobbar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs), vilket är mer effektivt för att kontinuerligt lägga till nya händelse data.

## <a name="how-to-update"></a>Så här uppdaterar du

Du behöver bara göra uppdateringar om du har anpassat verktyg som matar in dessa loggfiler för ytterligare bearbetning. Om du använder ett externt logg analys-eller SIEM-verktyg rekommenderar vi att du [använder Event Hub för att mata in dessa data i stället](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). Integrering av Event Hub är enklare när det gäller bearbetning av loggar från många tjänster och bok märkes plats i en viss logg.

Anpassade verktyg bör uppdateras för att hantera både det aktuella formatet och JSON-linje formatet som beskrivs ovan. Detta säkerställer att dina verktyg inte försvinner när data börjar visas i det nya formatet.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [att arkivera resurs resurs loggar till ett lagrings konto](./../../azure-monitor/platform/archive-diagnostic-logs.md)
* Lär dig mer om [att arkivera aktivitets logg data till ett lagrings konto](./../../azure-monitor/platform/archive-activity-log.md)

