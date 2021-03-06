---
title: Säkerhetskontroller
description: En check lista över inbyggda säkerhets kontroller för utvärdering av Azure Resource Managers tjänsten.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: b2916a87c5cef01605054569285362fcf1c649c8
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147563"
---
# <a name="security-controls-for-azure-resource-manager"></a>Säkerhets kontroller för Azure Resource Manager

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Resource Manager.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar | Ja |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | HTTPS/TLS. |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Saknas | Azure Resource Manager lagrar inget kund innehåll, endast kontroll data. |
| Kryptering på kolumn nivå (Azure Data Services)| Ja | |
| Krypterade API-anrop| Ja | |

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Nej | |
| Stöd för VNet-injektering| Ja | |
| Stöd för nätverks isolering och brand vägg| Nej |  |
| Stöd för Tvingad tunnel trafik| Nej |  |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Nej | |
| Loggning och granskning av kontroll-och hanterings plan| Ja | Aktivitets loggar visar alla Skriv åtgärder (placering, POST, DELETE) som utförs på resurserna. Se [Visa aktivitets loggar för att granska åtgärder på resurser](resource-group-audit.md). |
| Loggning och granskning av data planet| Saknas | |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | [Azure Active Directory](/azure/active-directory) baserad.|
| Auktorisering| Ja | |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja |  |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).