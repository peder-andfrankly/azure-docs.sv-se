---
title: Säkerhets kontroller för Azure Service Bus meddelanden
description: En check lista över säkerhets kontroller för utvärdering av Azure Service Bus meddelanden
services: service-bus-messaging
ms.service: service-bus-messaging
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: d0118f815a2ceb149c62363fa334c16d28c6d615
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894408"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Säkerhets kontroller för Azure Service Bus meddelanden

I den här artikeln dokumenteras säkerhets kontroller som är inbyggda i Azure Service Bus meddelanden.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Anteckningar | Dokumentation |
|---|---|--|--|
| Stöd för tjänst slut punkt| Ja (endast Premium-nivå) | VNet-tjänstens slut punkter stöds endast för [Service Bus Premium-nivån](service-bus-premium-messaging.md) . |  |
| Stöd för VNet-injektering| Nej | |  |
| Stöd för nätverks isolering och brand vägg| Ja (endast Premium-nivå) |  |  |
| Stöd för Tvingad tunnel trafik| Nej |  |  |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Stöds via [Azure Monitor och aviseringar](service-bus-metrics-azure-monitor.md). |  |
| Loggning och granskning av kontroll-och hanterings plan| Ja | Det finns tillgängliga åtgärds loggar.  | [Service Bus diagnostikloggar](service-bus-diagnostic-logs.md) |
| Loggning och granskning av data planet| Nej |  |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Autentisering| Ja | Hanteras via [Azure Active Directory hanterad tjänstidentitet](service-bus-managed-service-identity.md).| [Service Bus autentisering och auktorisering](service-bus-authentication-and-authorization.md). |
| Autentisering| Ja | Stöder auktorisering via [RBAC](authenticate-application.md) och SAS-token. | [Service Bus autentisering och auktorisering](service-bus-authentication-and-authorization.md). |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Anteckningar | Dokumentation |
|---|---|--|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar |  Ja för kryptering på Server sidan – rest som standard. |  |  |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Ja. För hands version | En kundhanterad nyckel i Azure-valv kan användas för att kryptera data i namn området Service Bus i vila. | [Konfigurera Kundhanterade nycklar för att kryptera Azure Service Bus data i vila med hjälp av Azure Portal (förhands granskning)](configure-customer-managed-key.md)  |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte | |   |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | Stöder standard-HTTPS/TLS-mekanismen. |   |
| Krypterade API-anrop| Ja | API-anrop görs via [Azure Resource Manager](../azure-resource-manager/index.yml) och https. |   |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | Stöder versions hantering av resurs leverantörer via [Azure Resource Manager API](/rest/api/resources/).|   |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).
