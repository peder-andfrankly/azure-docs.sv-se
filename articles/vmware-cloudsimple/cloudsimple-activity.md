---
title: Azure VMware-lösning av CloudSimple-CloudSimple – aktivitets hantering
description: Beskriver aktivitets koncept i VMware-lösningen av CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ad7905244ca190badf73c9a812a8f72282cbcdf3
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563185"
---
# <a name="activity-management-overview"></a>Översikt över aktivitets hantering

CloudSimple håller reda på all aktivitet som kan påverka hur din privata moln miljö fungerar. Aktiviteterna omfattar aviseringar, händelser, aktiviteter och gransknings aktiviteter. [Aktivitets sidorna](monitor-activity.md) sammanfattar all aktuell aktivitet och gör det möjligt att öka detalj nivån för ytterligare information.

## <a name="events"></a>Events

Händelser spårar användar-och system aktivitet på CloudSimple-portalen. Händelser visar den aktivitet som är associerad med en speciell resurs och påverkannas allvarlighets grad.  Du kan visa händelserna från CloudSimple-portalen.

## <a name="alerts"></a>Aviseringar

Aviseringar är aviseringar om viktiga aktiviteter i din CloudSimple-miljö. Händelser som påverkar fakturerings-eller användar åtkomst visas som aviseringar.  Du kan bekräfta aviseringar från CloudSimple-portalen.

## <a name="tasks"></a>Aktiviteter

Uppgifter spårar alla användar åtgärder som tar mer än 30 sekunder att slutföra. Du kan övervaka förloppet för en aktivitet från CloudSimple-portalen.  För slutförda uppgifter omfattar informationen den totala tiden för slut för ande.

## <a name="audit"></a>Granska

Gransknings loggar håller reda på användar åtgärder. Gransknings loggar innehåller de parametrar som har angetts för åtgärden av användaren.  Du kan använda gransknings loggar för att övervaka användar aktivitet för alla användare.

## <a name="next-steps"></a>Nästa steg

* [Visa konto sammanfattningen](account.md)