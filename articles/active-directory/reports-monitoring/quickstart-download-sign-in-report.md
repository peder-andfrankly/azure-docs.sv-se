---
title: Snabbstart Ladda ned en inloggningsrapport med Azure-portalen | Microsoft Docs
description: Lär dig hur du laddar ned en inloggningsrapport med Azure-portalen
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 9131f208-1f90-4cc1-9c29-085cacd69317
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cbd71696c3508a464b1343d552cba4a3391066f
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989659"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>Snabbstart: Hämta en inloggningsrapport med Azure-portalen

I den här snabbstarten får du lära dig hur du hämtar inloggningsloggarna för din klient för de senaste 24 timmarna. Du kan hämta upp till 250 000 poster från Azure Portal. Posterna sorteras efter senaste så att du får de senaste 250 000 posterna som standard. 

## <a name="prerequisites"></a>Förutsättningar

Du behöver:

* En Azure Active Directory-klient, med en premiumlicens för att visa inloggningsrapporten. Se [Kom igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera din Azure Active Directory-version. Observera att om du inte har några aktivitetsdata före uppgraderingen tar det ett par dagar innan data visas i rapporterna när du har uppgraderat till en premiumlicens.
* En användare som har rollen **säkerhetsadministratör,** ,  **säkerhetsläsare**, **rapportläsare** eller **global administratör** för klienten. Dessutom kan alla användare i klienten kan komma åt sina egna inloggningsloggar.

## <a name="quickstart-download-a-sign-in-report"></a>Snabbstart: Ladda ned en inloggningsrapport

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Välj **Azure Active Directory** från det vänstra navigeringsfönstret och använd **Växla katalog** för att välja din active directory.
3. Från instrumentpanelen väljer du **Azure Active Directory** och sedan **Inloggningsloggar**. 
4. Välj **senaste 24 timmarna** i filterlistrutan **datum** och välj **Tillämpa** för att visa inloggningsloggarna för de senaste 24 timmarna. 
5. Välj knappen **Ladda ned** , Välj **CSV** som fil format och ange ett fil namn för att ladda ned en CSV-fil som innehåller de filtrerade posterna. 

![Rapportering](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>Nästa steg

* [Rapporter om inloggningsaktiviteter i Azure Active Directory-portalen](concept-sign-ins.md)
* [Azure Active Directory-rapporteringskvarhållning](reference-reports-data-retention.md)
* [Azure Active Directory-rapporteringssvarstider](reference-reports-latencies.md)
