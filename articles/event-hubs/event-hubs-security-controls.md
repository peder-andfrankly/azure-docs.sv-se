---
title: Säkerhets kontroller för Azure Event Hubs
description: En check lista över säkerhets kontroller för utvärdering av Azure-Event Hubs
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 277a745d26961ed509258d5423fc3c0da9b79a24
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219403"
---
# <a name="security-controls-for-azure-event-hubs"></a>Säkerhets kontroller för Azure Event Hubs

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Event Hubs.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Anteckningar | Dokumentation |
|---|---|--|--|
| Stöd för tjänst slut punkt| Ja |  |  |
| Stöd för VNet-injektering| Nej | |  |
| Stöd för nätverks isolering och brand vägg| Ja |  |  |
| Stöd för Tvingad tunnel trafik| Nej |  |  |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | |  |
| Loggning och granskning av kontroll-och hanterings plan| Ja |  |  |
| Loggning och granskning av data planet| Ja |   |  |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Authentication| Ja | | [Ge åtkomst till Azure Event Hubs](authorize-access-event-hubs.md), [bevilja åtkomst till Event Hubs resurser med hjälp av Azure Active Directory](authorize-access-azure-active-directory.md), auktorisera [åtkomst till Event Hubs resurser med hjälp av signaturer för delad åtkomst](authorize-access-shared-access-signature.md) |
| Authorization|  Ja | | Autentisera [en hanterad identitet med Azure Active Directory för att få åtkomst till Event Hubs resurser](authenticate-managed-identity.md), [autentisera ett program med Azure Active Directory för att få åtkomst till Event Hubs resurser](authenticate-application.md), [autentisera åtkomst till Event Hubs resurser med hjälp av signaturer för delad åtkomst (SAS)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Anteckningar | Dokumentation |
|---|---|--|--|
| Kryptering på Server sidan på rest: Microsoft-hanterade nycklar |  Ja | |  |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Nej |  |  |
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte | |  |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | |  |
| Krypterade API-anrop| Ja |  |  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Anteckningar| Dokumentation |
|---|---|--|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja | |  |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).
